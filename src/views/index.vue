<template>
	<div class="content">
		<div class="conts">
			<div class="box">
				<div class="pointer" @click="chou()">
					<p>立即</p>
					<p>抽奖</p>
				</div>
				<div class="boxbg" :style="{transform:rotate_angle,transition:rotate_transition}">
					<div class="turn">
						<div :class="{'wheel-bg6':true}">
							<div class="prize-list">
								<ul class="ulOne" :class="{'win':indexa===0&&prize_list.length==3}">
									<li v-for="(item,index) in arrOne" :class="{'win':index===indexa}" :style="{ zIndex: item.zIndex,transform: item.deg }"></li>
								</ul>
								<ul class="ulTwo" :class="{'win':indexa===arrOne.length-1}">
									<li v-for="(item,index) in arrTwo" :class="{'win':index===indexb}" :style="{ zIndex: item.zIndex,transform: item.deg }"></li>
									<li v-if="prize_list.length==3" :class="{'win':indexb===0}" style="border:none;z-index:4; transform: rotate(329deg)"></li>
								</ul>
								<div></div>
							</div>
							<div class="prize-list">
								<div class="prize-item" v-for="(item,index) in prize_list" :key="index" :style="{transform:item.troter,width:item.width}">
									<div class="prize-pic">
										<img v-if="item.prizeType==0" src="../../static/img/xiexie.png">
										<img v-if="item.prizeType==1" src="../../static/img/lijian.png">
										<img v-if="item.prizeType==2" src="../../static/img/manjian.png">
										<img v-if="item.prizeType==3" src="../../static/img/gitfs.png">
									</div>
									<div class="prize-name">
										{{item.prizeName}}
									</div>
								</div>
							</div>
						</div>
					</div>
				</div>
			</div>

		</div>
		<div class="popup" v-show="toast_control">
			<div class="popbg"></div>
			<div class="popbox">
				<div class="img" :class="{'img1':false}"></div>
				<div class="words1">{{hasPrize.words1}}</div>
				<div class="words2">{{hasPrize.words2}}</div>
				<div class="words3" v-show="hasPrize.words3!=''">{{hasPrize.words3}}</div>
			</div>
			<div class="close" @click="toast_control=false"></div>
		</div>
	</div>
</template>

<script>
	export default {
		data() {
			return {
				winnum: '0', //中奖的下标
				indexa: '', //中奖的下标在第一个ul下li的index下标，背景变深颜色
				indexb: '', //中奖的下标在第二个ul下li的index下标，背景变深颜色
				toast_control: false, //抽奖结果和活动规则弹出框控制器
				click_flag: true, //是否可以旋转抽奖
				start_rotating_degree: 0, //转盘初始旋转角度
				rotate_angle: 0, //转盘将要旋转的角度
				start_rotating_degree_pointer: 0, //指针初始旋转角度
				rotate_angle_pointer: 0, //指针将要旋转的度数
				rotate_transition: "", //初始化选中的过度属性控制
				rotate_transition_pointer: "transform 12s ease-in-out", //初始化指针过度属性控制
				prize_list: [{
						prizeType: 1, // 奖品数量
						prizeName: "易趣豆积分豆豆积", // 奖品名称
					},
					{
						prizeType: 3,
						prizeName: "积分豆111",
					},
					{
						prizeType: 2,
						prizeName: "易趣豆222",
					},
			        {prizeType: 2,
			          prizeName: "易趣豆333"
			        },
			        {prizeType: 3,
			          prizeName: "易趣豆444"
			        },
//			        {prizeType: 3,
//			          prizeName: "易趣豆555"
//			        },
//			        {prizeType: 3,
//			          prizeName: "易趣豆666"
//			        },
//			        {prizeType: 3,
//			          prizeName: "易趣豆777"
//			        },
//			        {prizeType: 3,
//			          prizeName: "易趣豆888"
//			        }
				], //奖品列表
				arrOne: [],
				arrTwo: [],
				shopUid: '',
				userinfo: '',
				prizeinfo: {
					isGet: '',
					prizeInfos: [{
						validTime: '',
						invalidTime: '',
						type: ''
					}]
				}, //中奖优惠券或实物的信息
				hasPrize: {}, //抽奖之后弹出框信息
				nums:'0'
			}
		},
		mounted() {
			let _this = this;
			_this.setSan()
		},

		methods: {
			chou() {
				let _this = this;
				if(_this.click_flag){
					_this.indexa = ''
					_this.indexb = ''
					_this.nums=Number(_this.prize_list.length - 1)-Number(_this.winnum)
					_this.winnum = Math.floor(Math.random() * Number(_this.prize_list.length - 1));
					
					
					console.log(_this.winnum)
					console.log(_this.nums)
					_this.rotating(_this.winnum)
				}
				
			},
			setSan() {
				let _this = this
				_this.arrOne = [];
				_this.arrTwo = [];
				_this.rotate_angle = "rotate(" + Math.floor(-360 * 100 / _this.prize_list.length) / 200 + "deg)";
				_this.start_rotating_degree = Math.floor(-360 * 100 / _this.prize_list.length) / 200;
				for(var i = 0; i < _this.prize_list.length; i++) {
					_this.prize_list[i].troter = "rotate(" + Math.floor(360 * 100 / _this.prize_list.length) / 100 * (0.5 + Number(i)) + "deg) translateX(-50%)"
					_this.prize_list[i].width = Math.floor(3.14 * 5.6 / this.prize_list.length) + "rem"
					var item = {
						value: _this.prize_list[i].name,
						zIndex: Number(i) + 1,
						deg: "rotate(" + Math.floor(360 * 100 / this.prize_list.length) * i / 100 + "deg)",
						degnum: i,
					}
					if(i < _this.prize_list.length / 2) {
						_this.arrOne.push(item)
					} else {
						_this.arrTwo.push(item)
					}
				}
			},
			rotating(index) { //转盘转动函数，index值为中奖的下标，后台会返回中奖的id，这边会首先for循环判断中奖的下标
				let _this = this;
				_this.rotate_transition = "transform 6s cubic-bezier(0.25,0.1,0.01,1)";
				if(!_this.click_flag) return;
				var type = 0; // 默认为 0  转盘转动 1 箭头和转盘都转动(暂且遗留)
				var during_time = 5; // 默认为1s
				var result_index = index; // 最终要旋转到哪一块，对应prize_list的下标
				var rand_circle = 6; // 附加多转几圈，2-3
				_this.click_flag = false; // 旋转结束前，不允许再次触发
				if(type == 0) {
					if(this.start_rotating_degree < 0) {
						this.start_rotating_degree = 0
					} else {
						this.start_rotating_degree = this.start_rotating_degree - Math.floor(360 * 100 / _this.prize_list.length) / 200 -Math.floor(360 * 100 / this.prize_list.length) * _this.nums / 100;
						console.log(this.start_rotating_degree)
					}
					var rotate_angle = this.start_rotating_degree + 360 * 10 + Math.floor(-360 * 100 / this.prize_list.length) / 200 - Math.floor(360 * 100 / this.prize_list.length) * result_index / 100;
					this.start_rotating_degree = rotate_angle;
					_this.rotate_angle = "rotate(" + rotate_angle + "deg)";
					// 旋转结束后，允许再次触发
					setTimeout(function() {
						_this.click_flag = true;
						if(_this.winnum < _this.prize_list.length / 2) {
							_this.indexb = ''
							_this.indexa = _this.winnum;
						} else {
							_this.indexa = ''
							_this.indexb = _this.winnum - (_this.arrOne.length);
						}
						setTimeout(function() {
							_this.game_over();
						}, 100)
					}, during_time * 1000 + 1500); // 延时，保证转盘转完
				}
			},
			game_over() {
				let _this = this;
				_this.prizetype = 1
				if(_this.prizetype != -1) {
					_this.toast_control = true;
				} else {
					Alert.show("奖品已领完，下次请早到哦！")
				}
				var obj = {}
				if(_this.prize_list[_this.winnum].prizeType == 0) {
					obj = {
						type: 0,
						words1: '谢谢参与',
						words2: "不要气馁！",
						words3: '还有更多大奖等着你~'
					}
				} else if(_this.prize_list[_this.winnum].prizeType == 1 || _this.prize_list[_this.winnum].prizeType == 2) {
					obj = {
						type: 1,
						words1: '恭喜您！',
						words2: "获得" + _this.prize_list[_this.winnum].prizeName,
						words3: '可在“个人中心”查看'
					}
				} else if(_this.prize_list[_this.winnum].prizeType == 3) {
					obj = {
						type: 1,
						words1: '恭喜您！',
						words2: "获得" + _this.prize_list[_this.winnum].prizeName,
						words3: ''
					}
				}
				_this.hasPrize = obj
			},
		}

	}
</script>

<style scoped lang="scss">
	.content {
		.conts {
			min-height: 11.5rem;
			.activitytime {
				height: 0.5rem;
				width: 100%;
				position: absolute;
				.timebg {
					width: 100%;
					height: 100%;
					background: #000000;
					opacity: 0.3;
					position: absolute;
					left: 0;
					top: 0;
				}
				.time {
					color: #ffffff;
					line-height: 0.5rem;
					padding-left: 0.2rem;
					font-size: 0.24rem;
					position: relative;
					z-index: 2;
				}
			}
			.rule {
				height: 0.69rem;
				margin-bottom: 2.37rem;
				position: relative;
				.ruletop {
					width: 1.35rem;
					height: 0.46rem;
					position: absolute;
					right: 0;
					bottom: 3px;
					.rulebg {
						height: 100%;
						width: 100%;
						position: absolute;
						top: 0;
						left: 0;
						right: 0;
						background: #1201d5;
						opacity: 0.3;
						border-top-left-radius: 0.23rem;
						border-bottom-left-radius: 0.23rem;
					}
					.ruletitle {
						text-align: center;
						line-height: 0.46rem;
						font-size: 0.24rem;
						color: #fff;
						position: relative;
						z-index: 3;
						border-top-left-radius: 0.23rem;
						border-bottom-left-radius: 0.23rem;
						border-bottom: 3px solid #1201d5;
					}
				}
			}
			.title {
				height: 0.46rem;
				text-align: center;
				color: #464ff4;
				font-size: 0.28rem;
				line-height: 0.46rem;
				font-weight: 700;
				margin-bottom: 0.24rem;
			}
			.box {
				width: 6.68rem;
				height: 6.67rem;
				margin: auto;
				position: relative;
				overflow: hidden;
				.pointer {
					width: 1.81rem;
					height: 2.07rem;
					position: absolute;
					top: 50%;
					left: 50%;
					z-index: 20;
					transform: translate(-50%, -55%);
					background: url(../../static/img/pointer.png) top center no-repeat;
					background-size: 100%;
					p {
						font-size: 0.36rem;
						color: #ff4027;
						font-weight: 700;
						text-align: center;
					}
					p:first-of-type {
						margin-top: 0.6rem;
					}
				}
				.boxbg {
					width: 5.60rem;
					height: 5.60rem;
					background: url(../../static/img/box.png) top center no-repeat;
					background-size: 100%;
					padding: 0.54rem;
					.turn {
						width: 5.60rem;
						height: 5.60rem;
						border-radius: 50%;
						overflow: hidden;
						.slice {
							box-sizing: border-box;
							border: 1px solid #ffd8ad;
						}
						.win {
							background: #fff4c9!important;
						}
						.wheel-bg6 {
							width: 100%;
							height: 100%;
							position: relative;
							div {
								text-align: center;
							}
							.prize-list {
								width: 100%;
								height: 100%;
								position: absolute;
								ul {
									position: absolute;
									top: 0;
									left: 0;
									width: 100%;
									height: 100%;
									border-radius: 50%;
									overflow: hidden;
									margin: 0;
									padding: 0;
									li {
										position: absolute;
										top: 0px;
										left: 50%;
										width: 50%;
										height: 50%;
										transform-origin: 0 100%;
										overflow: hidden;
										cursor: pointer;
										box-sizing: border-box;
										border-left: 1px solid #ffd8ae;
										background: #fff;
									}
								}
								.ulOne {
									clip: rect(0, 5.6rem, 5.6rem, 2.8rem);
								}
								.ulTwo {
									clip: rect(0, 2.8rem, 5.6rem, 0);
								}
								.prize-item {
									position: absolute;
									top: 0;
									z-index: 12;
									height: 2.8rem;
									left: 50%;
									transform: translateX(-50%);
									transform-origin: 0 100%;
									.prize-pic {
										padding-top: 0.3rem;
										text-align: center;
										img {
											width: 0.75rem;
										}
									}
									.prize-name {
										font-size: 0.24rem;
										color: #ff6600;
									}
								}
							}
						}
					}
				}
				.money {
					position: absolute;
					width: 0.57rem;
					height: 0.55rem;
					top: 2.17rem;
					right: -0.2rem;
				}
			}
			.prizebox {
				margin: 0.3rem;
				position: relative;
				border-radius: 4px;
				overflow: hidden;
				padding: 0 0.27rem 0.28rem;
				.prizebg {
					position: absolute;
					border-radius: 4px;
					background: #fff;
					height: 100%;
					width: 100%;
					top: 0;
					left: 0;
					opacity: 0.2;
				}
				.pri {
					.titname {
						color: #fff;
						font-size: 0.32rem;
						text-align: center;
						margin: 0.07rem;
						span {
							img {
								width: 1.51rem;
								height: 0.14rem;
								margin: 0 0.2rem 0.03rem;
							}
						}
						.xuanz {
							img {
								transform: rotate(180deg);
							}
						}
					}
					.prizez {
						width: 100%;
						border: 1px solid #fa54a7;
						border-radius: 4px;
						background: #fff;
						display: flex;
						.couponleft {
							padding-left: 0.3rem;
							width: 3.9rem;
							position: relative;
							height: 100%;
							.iconright {
								div {
									line-height: 1.1;
								}
								.couponname {
									font-size: 0.32rem;
									color: #333333;
									margin: 0.45rem 0 0.2rem;
								}
								.timevalidity {
									font-size: 0.24rem;
									color: #999999;
									margin-bottom: 0.2rem;
								}
								.timevalidity:last-of-type {
									margin-bottom: 0.45rem;
								}
							}
						}
						.couponright {
							flex: 1;
							line-height: 1.2rem;
							text-align: center;
							font-size: 0.24rem;
							color: #ff4745;
							vertical-align: top;
							position: relative;
							.money {
								position: relative;
								bottom: 0.02rem;
								margin-right: 0.02rem;
							}
							.moneynum {
								font-size: 0.5rem;
							}
							a {
								position: absolute;
								left: 0;
								right: 0;
								bottom: 0.3rem;
								width: 1.5rem;
								height: 0.54rem;
								color: #fff;
								background: #ff4745;
								border-radius: 2px;
								line-height: 0.54rem;
								text-align: center;
								font-size: 0.28rem;
								margin: auto;
								display: block;
							}
							.tel {
								bottom: 0;
								top: 0;
							}
							div {
								position: absolute;
								right: -0.2rem;
								top: 0;
								bottom: 0;
								margin: auto;
								width: 1.28rem;
								height: 1.28rem;
								img {
									width: 100%;
									height: 100%;
								}
							}
						}
					}
				}
			}
		}
		.popup {
			width: 100%;
			height: 100%;
			position: fixed;
			z-index: 30;
			top: 0;
			left: 0;
			.popbg {
				width: 100%;
				height: 100%;
				background: #000;
				opacity: 0.7;
			}
			.popbox {
				width: 5.54rem;
				height: 4.98rem;
				position: absolute;
				top: 27%;
				left: 0;
				right: 0;
				margin: auto;
				background: url(../../static/img/giftboxbg.png) top center no-repeat;
				background-size: 100%;
				text-align: center;
				.img {
					width: 3.06rem;
					height: 1.4rem;
					margin: auto;
					margin-top: 1.07rem;
					margin-left: 1.1rem;
					background: url(../../static/img/gift.png) top center no-repeat;
					background-size: 100%;
				}
				.img1 {
					background: url(../../static/img/regret.png) top center no-repeat;
					background-size: 100%;
				}
				.words1 {
					font-size: 0.4rem;
					color: #ff7d12;
					margin-top: 0.2rem;
				}
				.words2 {
					font-size: 0.28rem;
					color: #ff8422;
				}
				.words3 {
					font-size: 0.24rem;
					color: #999999;
					margin-top: 0.07rem;
				}
			}
			.activityrule {
				width: 5.54rem;
				height: 6.32rem;
				position: absolute;
				top: 20%;
				left: 0;
				right: 0;
				margin: auto;
				background: url(../../static/img/rulebg.png) top center no-repeat;
				background-size: 100%;
				.rulecnt {
					padding: 1.3rem 0.77rem 0;
					p {
						font-size: 0.24rem;
						line-height: 0.4rem;
						text-align: justify;
						color: #333333;
					}
					p:last-of-type {
						color: #ff4928;
					}
				}
			}
			.close {
				width: 0.5rem;
				height: 0.5rem;
				background: url(../../static/img/close.png) top center no-repeat;
				background-size: 100%;
				position: absolute;
				top: 75%;
				left: 0;
				right: 0;
				margin: auto;
			}
		}
	}
</style>