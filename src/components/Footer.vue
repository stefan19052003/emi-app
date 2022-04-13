<template>
    <div class="fixed bottom-0 w-screen">
        <nav class="px-8 flex justify-between items-center gap-16 w-full h-14 bg-white rounded-t-2xl">
            <router-link to="/">
                <svg xmlns="http://www.w3.org/2000/svg" class="text-gray-600 hover:text-primary h-5 w-5" viewBox="0 0 20 20" fill="currentColor">
                <path d="M10.707 2.293a1 1 0 00-1.414 0l-7 7a1 1 0 001.414 1.414L4 10.414V17a1 1 0 001 1h2a1 1 0 001-1v-2a1 1 0 011-1h2a1 1 0 011 1v2a1 1 0 001 1h2a1 1 0 001-1v-6.586l.293.293a1 1 0 001.414-1.414l-7-7z" />
                </svg>
            </router-link>
            <router-link to="/">
            <svg xmlns="http://www.w3.org/2000/svg" class="text-gray-600 hover:text-primary h-5 w-5" viewBox="0 0 20 20" fill="currentColor">
                <path d="M4 3a2 2 0 100 4h12a2 2 0 100-4H4z" />
                <path fill-rule="evenodd" d="M3 8h14v7a2 2 0 01-2 2H5a2 2 0 01-2-2V8zm5 3a1 1 0 011-1h2a1 1 0 110 2H9a1 1 0 01-1-1z" clip-rule="evenodd" />
            </svg>
            </router-link>
            <router-link to="/">
            <svg xmlns="http://www.w3.org/2000/svg" class="text-gray-600 hover:text-primary h-5 w-5" viewBox="0 0 20 20" fill="currentColor">
                <path fill-rule="evenodd" d="M3 5a1 1 0 011-1h12a1 1 0 110 2H4a1 1 0 01-1-1zM3 10a1 1 0 011-1h12a1 1 0 110 2H4a1 1 0 01-1-1zM9 15a1 1 0 011-1h6a1 1 0 110 2h-6a1 1 0 01-1-1z" clip-rule="evenodd" />
            </svg>
            </router-link>
            <router-link to="/">
                <img class="w-6 h-6 rounded-full object-cover" src="https://images.unsplash.com/photo-1500648767791-00dcc994a43e?ixlib=rb-1.2.1&ixid=MnwxMjA3fDB8MHxzZWFyY2h8MTR8fHBvcnRyYWl0fGVufDB8fDB8fA%3D%3D&auto=format&fit=crop&w=700&q=60" alt="">
            </router-link>

        </nav>
    </div>

</template>

<script>
export default {
    setup () {
        

        return {}
    }
}
</script>

<style scoped>
</style>