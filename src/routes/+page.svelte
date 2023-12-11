<script>
import interact from 'interactjs'
import {joinRoom, selfId} from 'trystero'
import { onMount } from 'svelte';

const config = {appId: 'hypersend'}
let endX,endY
let otherPeer

onMount(() => {
		const room = joinRoom(config,'test')

			const [sendShape, getShape] = room.makeAction('color')

			room.onPeerJoin((peerId) => {
				console.log("peer joined:"+peerId)
				console.log("self:"+selfId)
				otherPeer = peerId
			})
			


	getShape((obj,peer) => {

		const img = document.createElement('img')
		img.src = obj.src
		

		if(obj.from == 'top'){
			img.style.animation = '1.5s linear slidefrombottom'
		}
		document.body.appendChild(img)
		img.width = 300
		img.style.left = 'calc(50% - 150px)'
		img.style.top = 'calc(50% - 150px)'
		
		img.classList.add('draggable')
		
		


		
	})


	const position = { x: 0, y: 0 }

	interact('.draggable').draggable({
	  inertia:true,
	  listeners: {
	    start (event) {
	      //console.log(event.type, event.target)
	    },
	    end(event){
	    	endX = event.client.x
	    	endY = event.client.y
	    	if(endX < 0){
	    		sendShape({src:event.target.src,from:'right'},otherPeer)
	    	}
	    	if(endX > window.innerWidth){
	    		sendShape({src:event.target.src,from:'left'},otherPeer)
	    	}
	    	if(endY < 0){
	    		sendShape({src:event.target.src,from:'top'},otherPeer)
	    	}
	    	if(endY > window.innerHeight){
	    		sendShape({src:event.target.src,from:'bottom'},otherPeer)
	    	}

	    },
	    move (event) {
	      position.x += event.dx
	      position.y += event.dy

	      event.target.style.transform =
	        `translate(${position.x}px, ${position.y}px)`

	    },
	  }
	})


})


	function showOnion(){
		document.getElementById('onion').style.display = 'block'
	}




</script>

<button on:click="{showOnion}">ONION</button>

<img src="/cat.jpg" style="display:none;" class="draggable" width="300" id="onion" />