<script lang="ts">
    import { pb } from "./pocketbase";

    
    export let author: string;
    export let content: string;
    export let createdAt: string = Date.now().toString();

    /**
     * Wether the message was sent by user or received
     */
     let sent: boolean = author === pb.authStore.model?.username;
     
    const capitalize = (s: string) => s.charAt(0).toUpperCase() + s.slice(1);
</script>

<div class="m-3 flex items-center {sent ? 'flex-row-reverse' : 'flex-row'}">
    <img
        src="https://avatar.iran.liara.run/username?username={author}&length=1"
        alt="avatar"
        class="w-10 h-10 rounded-lg"
    />
    <div class="flex flex-col">
        <p class="mx-3 font-light">
            {capitalize(author)} - {createdAt}
        </p>
        <p
            class="mx-3 p-2 rounded-lg overflow-ellipsis {sent
                ? 'bg-[#0b93f6] text-white'
                : 'bg-[#e5e5ea] text-black'}"
        >
            {content}
        </p>
    </div>
</div>
