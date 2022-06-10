<template>

<div>
  <div class=" flex justify-center mt-10 py-6" style="font-size:17px">
  <div class="shadow-xl p-2 bg-white rounded" style="width:800px">
    <h1 class="font-semibold mb-4 text-center"><span class="font-normal">socketid: </span> {{socketid}}</h1>
        <div class="text-indigo-600">Name</div>
        <div class="flex items-center"> <input @change="togglePrivate" class="mr-2" type="checkbox" :checked="private_">Private</div>
        <div v-if='private_'>
          <div>Enter room id</div>
           <input v-model="room" class="input border border-gray-400 appearance-none py-0 rounded w-full px-3 focus focus:border-indigo-600 focus:outline-none active:outline-none active:border-indigo-600" type="text" autofocus>
        </div>
        <div>Your Name </div>
        <input v-model="name" class="input mb-2 border border-gray-400 appearance-none py-0 rounded w-full px-3 focus focus:border-indigo-600 focus:outline-none active:outline-none active:border-indigo-600" type="text" autofocus>
        <div class="text-indigo-600">Enter your message</div>
        <input v-model="message" class="input border mb-2 border-gray-400 text-black appearance-none rounded w-full px-3 focus focus:border-indigo-600 focus:outline-none active:outline-none active:border-indigo-600" type="text" autofocus>
    <button @click.prevent="send" class="bg-indigo-600 hover:bg-blue-dark text-white py-1 font-bold px-2 rounded" style="font-size:17px">Send</button>
    <div>
 
  </div>
</div>
<div class="w-5/12 ml-8 h-full min-h-full">
  <div class="">
    <div>Messages</div>
       <div v-for="item in messages" :key="item._id" >
          <li class="py-1 flex w-full bg-gray-100 px-3 border border-gray-400 mb-3" style="min-height:30px"    >     
                <div class="mr-4 font-semibold">{{item.name}} : </div>
                <div>{{item.message}}</div>
          </li>
      </div>
  </div>
</div>


</div>

<div class="flex flex-col items-center mt-20 justify-center">
   <div>Room: {{this.roomsIjoined}}</div>
   <div>
    <input v-model="roomToJoin" class="input border mb-2 mt-6 border-gray-400 text-black appearance-none rounded w-5/12 px-3 focus focus:border-indigo-600 focus:outline-none active:outline-none active:border-indigo-600" type="text" autofocus>
    <button @click.prevent="joinRoom" class="bg-indigo-600 hover:bg-blue-dark text-white h-8 font-bold px-2 ml-6 rounded" style="font-size:17px">Join</button>
   </div>
  </div>
</div>

</template>

<script>
import {io} from "socket.io-client"
const socket = io('http://localhost:5000')

export default {
  name: 'IndexPage',
  data(){
    return {
      name: "Public",
      message: 'Hi Everyone',
      messages : [],
      socketid: '',
      private_: false,
      room: '',
      roomToJoin: '',
      roomsIjoined: '',
      users: [
        {
          name: 'Timothy',
          role: 'admin'
        },
        {
          name: 'Manos',
          role: 'user'
        }, 
        {
          name: 'Banjo',
          role: 'user'
        }
      ]
    }
  },
  async mounted(){
    socket.on('connect', () => {
      this.socketid = socket.id
      console.log('connected with id', socket.id)

      // socket.on('messages', (message)=>{
      // this.messages = message
      // console.log('messages', message)
      // })

      socket.on('message', (message)=>{
      this.messages.push(message)
      console.log('recieved', message)
      })

    })
  
   
  
  },
  computed: {
    
  },
  methods: {
    displayMsg(room){
      console.log({room, socket: this.socketid})
      return room === this.socketid || room === ''
    },
    togglePrivate(){
      this.private_ = !this.private_
      this.room = ''
    },
    send(){
        // if(this.name === 'Timothy'){
          
        // }else{

        // }

      let data = {name: this.name, message: this.message, room: this.room}
      socket.emit('input', { ...data})
      // console.log('message sent', data)
    },
    joinRoom(){
      this.roomsIjoined += ` ${this.roomToJoin}`
      console.log('join', this.roomToJoin)
      socket.emit('join-room', this.roomToJoin)
    }
  }
}
</script>
