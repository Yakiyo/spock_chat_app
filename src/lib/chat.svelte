<script lang="ts">
    import { onDestroy, onMount } from "svelte";
    import Message from "./message.svelte";
    import { currentUser, pb } from "./pocketbase";

    let newMessage: string;
    let unsubscribe: () => void;
    let messages: any[] = [];

    onMount(async () => {
        messages = await pb
            .collection("messages")
            .getList(1, 50, {
                sort: "created",
                expand: "author",
            })
            .then((x) => x.items)
            .catch((e) => {
                console.error(e);
                alert(`Unexpected error while fetching messages: ${e}`);
                return [];
            });

        unsubscribe = await pb
            .collection("messages")
            .subscribe("*", async ({ action, record }) => {
                console.log(record);
                if (action === "create") {
                    const author = await pb
                        .collection("users")
                        .getOne(record.author);

                    record.expand = { author };
                    messages = [...messages, record];
                    console.log(messages.at(-1));
                }

                if (action === "delete") {
                    messages = messages.filter((m) => m.id !== record.id);
                }
            });
            scrollDown();
    });

    const scrollDown = () => {
        console.log('scrolling')
        const chats = document.getElementById("chats")!;
        chats.scrollTop = chats.scrollHeight;
    }

    onDestroy(() => {
        unsubscribe();
    });

    async function sendMessage() {
        const data = {
            content: newMessage,
            author: $currentUser?.id,
        };

        try {
            await pb.collection("messages").create(data);
        } catch (error) {
            console.error(error);
            alert(error);
        } finally {
            newMessage = "";
        }
    }
</script>

<div on:change={scrollDown}
    id="chats"
    class="flex flex-col w-[70%] overflow-auto bg-white min-h-[60vh] max-h-[60vh] rounded-md py-4"
>
    {#each messages as message (message.id)}
        <Message
            content={message.content}
            author={message.expand.author.username}
        ></Message>
    {/each}
</div>
<form
    class="flex flex-row min-w-[70%] max-w-[70%] justify-around pt-2"
    on:submit|preventDefault={sendMessage}
>
    <input
        class="basis-10/12 p-5 bg-white rounded"
        type="text"
        id="message"
        placeholder="Message"
        bind:value={newMessage}
    />
    <button class="basis-2/12 bg-blue-700" type="submit">🕊️</button>
</form>
