<template>
    <h1>{{ msg }}</h1>
    <button @click="count++">count is: {{ count }}</button>
    <input type="text" v-model="usernameInput" />
    <h3>Searching for {{ usernameFetch }}</h3>
    <button @click="updateFetchUsername()">Fetch GitHub Stats</button>
    <hr />
    <ul>
        <li v-for="lib in data" :key="lib.name">{{ lib.name }}</li>
    </ul>
</template>

<script lang="ts">
import {
    computed,
    defineComponent,
    reactive,
    ref,
    toRefs,
    watchEffect,
} from "vue";

interface CompState {
    data: any[];
}

export default defineComponent({
    name: "HelloWorld",
    props: {
        msg: { type: String, required: true },
    },
    setup(props) {
        let count = ref(0);
        const usernameInput = ref("");
        let usernameFetch = ref("");
        const state = reactive<CompState>({ data: [] });

        // ==========================
        //          METHODS
        // ==========================

        const updateFetchUsername = () => {
            console.dir(usernameInput);
            console.dir(usernameFetch);
            usernameFetch.value = usernameInput.value;
        };

        // ==========================

        watchEffect(() => {
            if (usernameFetch.value) {
                fetch(
                    `https://api.github.com/users/${usernameFetch.value}/repos`
                )
                    .then((res) => res.json())
                    .then((data) => {
                        state.data = data;
                        console.dir(data);
                        usernameFetch.value = "";
                    });
            }
        });

        return {
            count,
            usernameInput,
            usernameFetch,
            updateFetchUsername,
            ...toRefs(state),
        };
    },
});
</script>
