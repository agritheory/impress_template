<template>
  <div
		id="impress"
		:style="scene"
		:key="currentIndex"
	>
    <slide 
      v-for="(slide, index) in slides"
      :conf="slide"
      :content="slide.content"
			:key="index"
    ></slide>
  </div>
  <div id="prompt" v-show="prompt">
    <p>Use Arrow keys to navigate</p>
  </div>
</template>

<style>
body {
  font-family: Helvetica, sans-serif;
  overflow: hidden;
  background-color: #e0e0e0
}

#impress {
  position:absolute;
  box-sizing:border-box;
  transition: all 1s ease-in-out;
}

a:link{
  color: black;
  text-decoration: none;
  background-color: whitesmoke;
  border-radius: 5px;
}

a:visited{
  color: black;
}

#prompt{
  font-size: 48px;
  text-align: center;
  background-color: rgba(0,0,0,0.15)
}
</style>

<script setup>
	import { computed, reactive, watchEffect, ref } from 'vue'
	import slide from './Slide.vue'
	import { marked } from 'marked'
	import fm from 'front-matter'


	let prompt = reactive(true)
	let currentIndex = reactive(0)
	let windowSize = {
		height: window.innerHeight,
		width: window.innerWidth
	}
	const props = defineProps({
		deck: String,
	})
	window.addEventListener('resize', () => resize())
	document.addEventListener('keydown', (e) => {
		console.log(e.keyCode)
		if (prompt) {
			prompt = false
		}
		switch(e.keyCode){
			case 37:
			case 38:
					previousSlide()
					e.preventDefault()
					break
			case 39:
			case 40:
					nextSlide()
					e.preventDefault()
					break
			default:
					return
		}
	})
	const nextSlide = () => {
		const max_idx = slides.length - 1
		const current = currentIndex
		if (current < max_idx) {
			currentIndex = current + 1
		} else {
			currentIndex = 0
		}
		console.log('current index', currentIndex)
	}
	const previousSlide = () => {
		const current = currentIndex
		if (current > 0) {
			currentIndex = current - 1
		} else {
			currentIndex = slides.length - 1
		}
	}
	const resize = () => {
			windowSize.height = window.innerHeight
			windowSize.width = window.innerWidth
	}

	const overviewConfig = {
		x: 0,
		y: 0,
		rotate: 0,
		scale: 1,
		style: {
			_width: 4300,
			_height: 3000,
			get width () {
				return `${this._width}px`
			},
			get height () {
				return `${this._height}px`
			},
			zIndex: -1
		}
	}

	const classicConfig = {
		style: {
			_width: 900,
			_height: 700,
			get width () { return this._width + 'px' },
			get height () { return this._height + 'px' },
			padding: '40px 60px',
			fontSize: '50px',
			lineHeight: '72px',
			letterSpacing: '-1px',
			border: '1px solid rgba(0,0,0.0.3)',
			borderRadius: '10px',
			boxShadow: '0 2px 6px rgba(0,0,0,.1)',
			backgroundColor: 'white',
			transitionDelay: '0.5s'
		}
	}
	const modernConfig = {
		style: {
			_width: 900,
			get width () {
				return `${this._width}px`
			},
			_height: 700,
			get height () {
				return `${this._height}px`
			},
			fontSize: '48px',
			lineHeight: '1.5'
		}
	}
	const configs ={
		modernConfig: modernConfig,
		classicConfig: classicConfig,
		overviewConfig: overviewConfig,
	}

	const renderedSlides = (deck) =>{
		let splitDeck = deck.split('---')
		let slides = []
		let slideNumber = 1
		for(let i=0; i<splitDeck.length; i+=1){
			if(i%2 == 0){
				continue
			}
			let slide = `---\n${splitDeck[i]}---\n ${splitDeck[i+1] || ''}\n`
			slide = fm(slide)
			slide.slideNumber = slideNumber
			slide.content = marked.parse(slide.body)
			if(!slide.config){
				slide = Object.assign(
					{...slide.attributes},
					classicConfig,
					{content: marked.parse(slide.body), slideNumber: slideNumber,}
				)
			}
			else{
				slide = Object.assign(
					{...slide.attributes},
					configs[slide.config],
					{content: marked.parse(slide.body), slideNumber: slideNumber,}
				)
			}
			slides.push(slide)
			slideNumber+=1
		}
		return slides	
	}

	const slides = renderedSlides(props.deck)

	watchEffect(() => {
		console.log(currentIndex.value)
	})

	const scene = computed(() => {
		const active = slides[currentIndex]

		const windowWidth = windowSize.width
		const windowHeight = windowSize.height
		const slideWidth = active.style._width
		const slideHeight = active.style._height

		// tranlate
		const x = active.x || 0
		const y = active.y || 0
		const _x = windowWidth * 0.5 - (x + slideWidth * 0.5)
		const _y = windowHeight * 0.5 - (y + slideHeight * 0.5)
		const _location = `translate(${_x}px,${_y}px)`

		// rotate
		const deg = active.rotate || 0
		const _rotate = deg !== 0 ? `rotate(${-deg}deg)` : ''

		// scale
		const size = active.scale || 1
		const compensation = Math.min(windowWidth / slideWidth, windowHeight / slideHeight)
		const _size = 1 / size * compensation * 0.8
		const _scale = _size !== 1 ? `scale(${_size})` : ''

		return {
			transform: [_location, _rotate, _scale].join(' '),
			transformOrigin: `${x + slideWidth * 0.5}px ${y + slideHeight * 0.5}px`
		}
	})

</script>
