<template>
	<div class="div">
		<div class="modal-vue-wrapper"  :class="[animationParent,{'modal-vue-wrapper-show' : open}]" v-show="isOpen">
			<div class="modal-vue-overlay" @click="$emit('hide')" :style="{backgroundColor:bgOverlay}"></div>
			<div class="modal-vue-panel" :class="[animationPanel,{'modal-vue-show':open}]" :style="{width:width,backgroundColor:bgPanel}">
				<div class="modal-vue-content">
					<div class="modal-vue-actions"> 
						<div class="modal-vue-action-close" @click="$emit('hide')">
							<svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24"><path d="M19 6.41L17.59 5 12 10.59 6.41 5 5 6.41 10.59 12 5 17.59 6.41 19 12 13.41 17.59 19 19 17.59 13.41 12z" fill="#292c34"></path></svg>
						</div>
					</div>
					<div class="modal-vue-content-panel">
						<slot></slot>
					</div>
				</div>
			</div>
		</div>
	</div>

</template>

<script>
export default {

	name: 'modal-vue-perfect',
	props: {
		visible: {
			type: Boolean,
			required: false,
			default: false
		},
		
		resizeWidth:{
			type:Object,
		},
		animationPanel:{
			type: String,
			required: false,
			default: 'modal-fade'
		},
		bgOverlay:{
			type: String,
			required: false,
			default: '#fff'
		},
		bgPanel:{
			type: String,
			required: false,
			default: '#fff'
		},
		animationParent:{
			type: String,
			required: false,
			default: 'modal-fade'
		},
		defaultWidth:{
			type: String,
			required: false,
			default: '80%'
		},
		closeScroll:{
			type: Boolean,
			required: false,
			default: true
		}
	},
	data () {
		return {
			width:this.defaultWidth || '80%',
			open:false,
			isOpen:false,
			backups: {
				body: {
					height: null,
					overflow: null,
					paddingRight:null
				}
			}
		}
	},
	watch:{ 
		visible(val) {
			if(val){
				this.isOpen=true
				setTimeout(() => this.open = true, 30)
				if(this.closeScroll){
					this._lockBody()
				}
			}else{
				if(this.closeScroll){
					this._unlockBody()
				}
				this.open=false
				setTimeout(() => this.isOpen = false, 300)			
			}
		}
	},

	beforeDestroy() {
		window.removeEventListener('resize', this.getWindowWidth);
		window.removeEventListener('resize', this.getWindowHeight);
	},
	mounted(){
		this.$nextTick(function() {
		window.addEventListener('resize', this.getWindowWidth);
		window.addEventListener('resize', this.getWindowHeight);
		  this.getWindowWidth()
		  this.getWindowHeight()
		})
		
	},
	methods:{
		getWindowHeight(event) {
			this.windowHeight = document.documentElement.clientHeight;
			},
			getWindowWidth(event) {
				if (this.resizeWidth && Object.keys(this.resizeWidth).length > 0) {

						this.windowWidth = document.documentElement.clientWidth;
						var filter = Object.keys(this.resizeWidth).find(f=> f >= this.windowWidth)
						if (filter) {
							 this.width = this.resizeWidth[filter]
						}
						else{
							this.width = this.defaultWidth
						}
					}
		},
		_lockBody() {
				this.backups.body.height = document.body.style.height
				this.backups.body.overflow = document.body.style.overflow
				document.body.style.paddingRight = '15px'
				document.body.style.height = '100%'
				document.body.style.overflow = 'hidden'
			},
			_unlockBody() {
				document.body.style.height = this.backups.body.height
				document.body.style.overflow = this.backups.body.overflow
				document.body.style.paddingRight = this.backups.body.paddingRight
			},
	}
}
</script>

<style lang="less">
.modal-vue-wrapper.modal-fade{
  opacity: 0.1;
  visibility: hidden;
}
.modal-vue-wrapper.modal-scale{
	 -webkit-transform: scale(-1,1);
    -ms-transform: scale(-1, 1);
    -o-transform: scale(-1, 1);
    transform: scale(-1, 1);
}
.modal-vue-wrapper{
	position: fixed;
	top: 0;
	left: 0;
	width: 100vw;
	height: 100vh;
	font-size: 14px;
	-webkit-font-smoothing: antialiased;
	z-index: 9999999999;
    -webkit-transform: translate3D(0,0,0);
    -ms-transform: translate3D(0,0,0);
    -o-transform: translate3D(0,0,0);
    transform: translate3D(0,0,0);
  
    transition:all 0.2s cubic-bezier(0.52, 0.02, 0.19, 1.02);
	.modal-vue-overlay{
		position: fixed;
		top: 0;
		left: 0;
		width: 100vw;
		height: 100vh;
		z-index: 9001;
		font-size: 14px;
		-webkit-font-smoothing: antialiased;
		// background: rgba(23, 23, 23, 0.9);
		z-index: 9999999999;
	}

	.modal-vue-panel.modal-fade{
		transform:scale(1) translate(0, -50%);
	}
	.modal-vue-panel.modal-rotate{
		transform:rotate(45deg) translate(0, -50%);
	}
	.modal-vue-panel.modal-slide-right{
		transform:translate(100px, -50%);
	}
	.modal-vue-panel.modal-slide-left{
		transform:translate(-100px, -50%);
	}
	.modal-vue-panel.modal-slide-top{
		transform:translate(0, -100%);
	}
	.modal-vue-panel.modal-slide-bottom{
		transform:translate(0, 100%);
	}

	.modal-vue-panel{

		z-index: 99999999999;
		-moz-box-sizing: border-box;
		box-sizing: border-box;
		background: #fff;
		box-shadow: 0px 8px 46px rgba(0, 0, 0, 0.08), 0px 2px 6px rgba(0, 0, 0, 0.03);
		position: absolute;
		max-height: 100vh;
		overflow-y: auto;
		border-radius: 2px;
    	top: 50%;
	    left: 0;
	    right: 0;
	    margin:0 auto;
		
		opacity: 0;
		transition-property: transform, opacity, width ;
		transition-duration:0.3s;
		 /* // 0.3s;  */
		transition-delay: 0.05s;
		transition-timing-function: cubic-bezier(0.52, 0.02, 0.19, 1.02);
			&::-webkit-scrollbar-track
				{
					-webkit-box-shadow: inset 0 0 6px rgba(0,0,0,0.3);
					background-color: #F5F5F5;
				}
				&::-webkit-scrollbar
				{
					width: 6px;
					height: 5px;
					background-color: #F5F5F5;
				}
				&::-webkit-scrollbar-thumb
				{
					background-color: #41b9d2;
				} 

		.modal-vue-actions{
			position: absolute;
			top: 12px;
			right: 12px;
			.modal-vue-action-close{
				display: inline-block;
				cursor: pointer;
				color: #222C38;
				text-align: center;
				width: 42px;
				height: 42px;
				line-height: 42px;
				border-radius: 50%;
			}
		}
		.modal-vue-content{

			display: flex;
			align-items: center;
			padding-left: 15px;
			padding-right: 15px;
			padding-top: 24px;
			padding-bottom: 24px;
			line-height: 1.5;
			
			.modal-vue-content-panel{
				display: block;
				text-align:justify;
				font-size: 16px;
				padding-top: 5px;
				padding-bottom: 10px;
				flex-grow: 1;
	

			}
		}
	}
	.modal-vue-show{
		transform: translate(0, -50%) !important;
		opacity: 1 !important;

	}
}
.modal-vue-wrapper-show,.modal-vue-wrapper-show.modal-fade ,.modal-vue-wrapper-show.modal-scale{
	visibility: visible;
    opacity: 1;
	background: rgba(255, 255, 255, 0.9);
	-webkit-transform: translate3D(0,0,0);
    -ms-transform: translate3D(0,0,0);
    -o-transform: translate3D(0,0,0);
    transform: translate3D(0,0,0);

}
</style>