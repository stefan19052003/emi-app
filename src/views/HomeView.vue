<template>
  <div class="home relative">
    <div class="w-screen fixed bg-gradient-to-b from-platinum to-primary h-screen p-8">
      <div class="flex justify-between items-center">
        <img class="w-10 h-10" src="@/assets/logo.png" alt="Logo">
        <div class="relative">
          <img class="w-10 h-10 rounded-full object-cover" src="https://images.unsplash.com/photo-1500648767791-00dcc994a43e?ixlib=rb-1.2.1&ixid=MnwxMjA3fDB8MHxzZWFyY2h8MTR8fHBvcnRyYWl0fGVufDB8fDB8fA%3D%3D&auto=format&fit=crop&w=700&q=60" alt="">
          <span class="bottom-0 left-7 absolute  w-3.5 h-3.5 bg-green-400 border-2 rounded-full"></span>
        </div>
      </div>
      <p class="text-3xl text-primary font-bold text-left pt-8">Hello,</p>
      <p class="text-3xl text-primary font-black text-left">Driver 123</p>
      <p class="text-sm text-primary text-left">5 Packages ready to be delivered.</p>
    </div>
    <div class="w-screen h-screen p-5 bg-platinum absolute top-56 rounded-t-3xl shadow-2xl shadow-primary ">
      <router-link to="/journey" class="text-white flex gap-5 items-center justify-center p-4 px-6 max-w-sm bg-primary rounded-2xl border border-gray-200 shadow-md">
        <svg xmlns="http://www.w3.org/2000/svg" class="h-10 w-10" viewBox="0 0 20 20" fill="currentColor">
          <path d="M8 16.5a1.5 1.5 0 11-3 0 1.5 1.5 0 013 0zM15 16.5a1.5 1.5 0 11-3 0 1.5 1.5 0 013 0z" />
          <path d="M3 4a1 1 0 00-1 1v10a1 1 0 001 1h1.05a2.5 2.5 0 014.9 0H10a1 1 0 001-1V5a1 1 0 00-1-1H3zM14 7a1 1 0 00-1 1v6.05A2.5 2.5 0 0115.95 16H17a1 1 0 001-1v-5a1 1 0 00-.293-.707l-2-2A1 1 0 0015 7h-1z" />
        </svg>
        <h5 class="text-centermb-2 text-xl font-bold tracking-tight">Start today's journey.</h5>
      </router-link>
      <div class="py-4">
        <div class="flex justify-between items-end text-primary">
          <p class="font-black text-2xl">Others</p>
          <p class="text-sm text-green-500">See all></p>
        </div>
        <div class="grid grid-cols-2 grid-rows-2 gap-8 pt-3">
          <div v-for="x in 4" :key="x" class="w-full h-40 flex flex-col items-center p-3 bg-white shadow-md rounded-xl">
            <p class="font-bold text-md text-primary">Other Mode</p>
            <svg xmlns="http://www.w3.org/2000/svg" class="h-24 w-24 text-primary" viewBox="0 0 20 20" fill="currentColor">
              <path d="M4 3a2 2 0 100 4h12a2 2 0 100-4H4z" />
              <path fill-rule="evenodd" d="M3 8h14v7a2 2 0 01-2 2H5a2 2 0 01-2-2V8zm5 3a1 1 0 011-1h2a1 1 0 110 2H9a1 1 0 01-1-1z" clip-rule="evenodd" />
            </svg>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
// @ is an alias to /src
import HelloWorld from '@/components/HelloWorld.vue'

export default {
  name: 'HomeView',
  components: {
    HelloWorld
  }
}
</script>
