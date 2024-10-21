<script lang="ts">
    import { pb } from "./pocketbase";

    let username: string;
    let password: string;

    async function login() {
        username = username.toLowerCase();
        await pb
            .collection("users")
            .authWithPassword(username, password)
            .catch((e) => {
                console.error(e);
                alert(`Unexpected error while signing up: ${e}`);
            });
    }

    async function signup() {
        username = username.toLowerCase();
        try {
            const data = {
                username,
                password,
                passwordConfirm: password,
            };

            await pb.collection("users").create(data);
            await login();
        } catch (error) {
            console.error(error);
            alert(`Unexpected error while signing up: ${error}`);
        }
    }

    const buttons: {
        text: string;
        onClick: () => void;
    }[] = [
        {
            text: "Sign Up",
            onClick: signup,
        },
        {
            text: "Login",
            onClick: login,
        },
    ];
</script>

<div class="p-10 rounded bg-white">
    <form on:submit|preventDefault>
        <label class="inline-block w-[100%] pb-3" for="username">Username</label
        >
        <input
            class="inline-block w-[100%] pb-3 mb-3 border-solid border border-black rounded"
            type="text"
            id="username"
            bind:value={username}
        />

        <label class="inline-block w-[100%] pb-3" for="password">Password</label
        >
        <input
            class="inline-block w-[100%] pb-3 mb-3 border-solid border border-black rounded"
            type="password"
            id="password"
            bind:value={password}
        />

        <div class="flex flex-row justify-around pt-10">
            {#each buttons as button }
            <button
            class="border border-black px-3 py-2 rounded text-white bg-gray-800 hover:bg-black hover:font-bold"
            on:click={button.onClick}>{button.text}</button
        >
            {/each}
        </div>
    </form>
</div>
