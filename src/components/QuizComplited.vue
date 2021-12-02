<template>
    <div class="w-screen h-screen absolute z-30 bg-white bg-opacity-70 flex justify-center items-center">
        <div class="bg-green-700 p-4">
            <p class="font-bold text-white text-2xl">Your Score</p>
            <p class="my-4 font-bold text-3xl text-center text-white">{{correct}}/{{allQuestion}}</p>
        </div>
    </div>
</template>

<script>
export default {
    setup() {

    },
    props: {
        correct: Number,
        allQuestion: Number
    }
}
</script>