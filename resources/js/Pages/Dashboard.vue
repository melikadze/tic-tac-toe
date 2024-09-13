<template>
    <Head title="Dashboard" />

    <AuthenticatedLayout>
        <template #header>
            <h2 class="font-semibold text-xl text-gray-800 leading-tight">Dashboard</h2>
        </template>

        <div class="py-12">
            <div class="max-w-7xl mx-auto sm:px-6 lg:px-8">
                <div class="bg-white overflow-hidden shadow-sm sm:rounded-lg">
                    <Link :href="route('games.store')" method="post" class="py-4 px-6 block">
                        <PrimaryButton>
                            Create Game
                        </PrimaryButton>
                    </Link>

                    <ul class="divide-y divide-gray-200">
                        <li v-for="game in games" :Pkey="game.id" class="py-1.5 px-2 flex justify-between items-center">
                            <span class="text-gray-500">
                                {{ game.player_one.name }}
                            </span>
                            <Link :href="route('games.join', game.id)" as="button" method="post" >
                                <SecondaryButton>
                                    Join Game
                                </SecondaryButton>
                            </Link>
                        </li>
                    </ul>
                </div>
            </div>
        </div>
    </AuthenticatedLayout>
</template>

<script setup lang="ts">
import PrimaryButton from '@/Components/PrimaryButton.vue';
import SecondaryButton from '@/Components/SecondaryButton.vue';
import AuthenticatedLayout from '@/Layouts/AuthenticatedLayout.vue';
import { Head, Link , router} from '@inertiajs/vue3';
import { ref } from 'vue';


interface Game {
    id: number;
    player_one: Object;
}

interface GameJoinedEvent {
    game: Game;
}

const props = defineProps(['games']);
const games = ref(props.games.data);


window.Echo.private('lobby').listen('GameJoined', (event: GameJoinedEvent) => {
    games.value = games.value.filter((game: Game) => game.id !== event.game.id);

    console.log(event);

    if (games.value.length < 100) {
        router.reload({ only: ['games'], onSuccess: () => games.value = props.games.data });
    }
});
</script>