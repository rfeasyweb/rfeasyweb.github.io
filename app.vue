<template>
	<div class="wrapper">
		<div class="coat_wrap" v-bind:style="{background:'repeating-linear-gradient( transparent, transparent '+(Number(rectHeight) - 2) +'px, lightgray 0, lightgray '+ rectHeight +'px ), repeating-linear-gradient(90deg, transparent, transparent '+ (Number(rectWidth) - 2) +'px, lightgray 0, lightgray '+ rectWidth +'px )'}">
			<svg xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink"
			width="500"
			height="500"
			viewBox="0 0 500 500"
			v-on:click="svgClick($event)"
			>

				<circle r="4"
				:cx="points.x"
				:cy="points.y"
				fill="#fff" stroke="#f00" stroke-width="2" 
				v-for="(points, index) in coatPoints"
				v-if="coatPoints.length" />

				<path :d="svgPath + done"
				stroke-width="1" stroke="#222" fill="transparent"
				v-if="coatPoints.length" />


				<clipPath id="clip_path_wo">
					<path :d="svgPath + done"></path>
				</clipPath>
				<g v-if="!!done" clip-path="url(#clip_path_wo)">
					<defs>
						<rect id="backRect" x="0" y="0" 
						:width="rectWidth - 1"
						:height="rectHeight - 1"
						fill="red"
						stroke="#d3d3d3"
						stroke-width="1"/>
						<rect id="backRect1" x="0" y="0" 
						:width="rectWidth - 1"
						:height="rectHeight - 1"
						fill="green"
						stroke="#d3d3d3"
						stroke-width="1"/>
					</defs>
		
					<use 
					v-for="(points) in coatGridFunc"
					xlink:href="#backRect"
					:x="points.x-1"
					:y="points.y-1"
					/>
					
				</g>

			</svg>
		</div>
		<div class="input_wrap">
			<div class="form-group">
				<label>Ширина клетки{{rectWidth}}</label>
				<input 	type="text" 
						class="form-control"
						v-model="rectWidth"
						>
			</div>
			<div class="form-group">
				<label>Высота клетки</label>
				<input 	type="text" 
						class="form-control"
						v-model="rectHeight"
						>
			</div>
			<div class="form-check">
				<label class="form-check-label">
					<input class="form-check-input" 
					type="checkbox" 
					v-model="pathLT"
					> 
					линия/кривая
				</label>
			</div>
			<div class="form-group">
				<button class="btn btn-primary" :disabled="!!done"
					@click="done = 'z'">
					закончить
				</button>
			</div>
			<div class="form-group">
				<button class="btn btn-primary"
					@click="coatPoints = []">
					очистить
				</button>
			</div>
		</div>
	</div>
	
</template>

<script>

	export default {
		data(){
			return{
				rectWidth:"100",
				rectHeight:"100",
				coatPoints: [],
				pathLT : false,
				done: ""
			}
		},
		methods: {
			svgClick($event){
				if (($event.target.tagName == "svg") && !this.done) {
					let x = $event.offsetX,
						y = $event.offsetY

					this.coatPoints.push({'x': x, 'y': y})
				}else if ($event.target.tagName == "use") {
					$event.target.href.baseVal = "#backRect1";
					// $event.target.href.animVal = "#backRect1";
				}
				console.log($event);
			}
		},
		computed: {
			svgPath: function() {
				if (this.coatPoints.length){
					let path ='M ' + this.coatPoints[0].x + ' ' + this.coatPoints[0].y;
					if (this.coatPoints.length > 2){
						for (let i = 1; i < this.coatPoints.length; i++){
							path += ' '+ this.pathLTFunc +' ' + this.coatPoints[i].x + ' ' + this.coatPoints[i].y;
						}
					}
					console.log(path);
					return path;
				}
			},
			coatGridFunc: function() {
				let coatGrid = [];

				for (let i = 0; i < 500; i+=Number(this.rectHeight)) {
					for (let k = 0; k < 500; k+=Number(this.rectWidth)) {
						coatGrid.push({"x":k, "y":i});
					}
				}
				console.log(coatGrid);
				return coatGrid;
			},
			pathLTFunc: function() {
				let lt = "L";
				if (this.pathLT) {
					lt = "T";
				}
				return lt;
			}
		}
	}
</script>

<style scoped>
.wrapper{
	width: 100%;
	height: 100vh;
	display: flex;
	align-items: center;
	justify-content: center;
}
.coat_wrap{
	font-size: 0;
}
.coat_wrap SVG{
	border: 1px solid;
	margin: 0 auto;
	/*width: 100%;*/
	/*height: 100%;*/
}
.input_wrap{
	padding: 10px;
}
</style>
