
<script lang="ts">
    import { emojis } from './emoji';

    type State = 'start' | 'playing' | 'pause' | 'won' | 'lost';
    let state:State = 'start';
    let size = 20;
    let grid = createGrid();

    let maxMatches = grid.length / 2;
    let selected: number[] = [];
    let matches: string[] = [];

    function createGrid() {
        let cards = new Set<string>();
        let maxSize = size / 2;
        while (cards.size < maxSize) {
            const randomIndex = Math.floor(Math.random() * emojis.length);
            cards.add(emojis[randomIndex]);
        }
        return shuffle([...cards, ...cards]);
    }

    function shuffle<Item>(array: Item[]) {
        return array.sort(() => Math.random() - 0.5);
    }

    function selectCard(cardIndex: number) {
        selected = selected.concat(cardIndex);
     };

    function matchCards() { 
        const [first, second] = selected;

        if (grid[first] === grid[second]) {
            matches = matches.concat(grid[first]);
        }

        setTimeout(() => {
            selected = [];
        }, 300);
    };

    function gameWon() {
        state = 'won';
     };

    $: selected.length === 2 && matchCards();

    $: maxMatches === matches.length && gameWon();

    $: console.log(state, selected, matches);

</script>

{#if state === 'start'}
   <h1> Matching Game </h1>
   <button on:click={() => state = 'playing'}> Play </button>
{/if}

{#if state === 'playing'}

    <div class="matches">
        {#each matches as matchCard}
            <div>{ matchCard }</div>
        {/each}
    </div>

    <div class="cards">
        {#each grid as card, cardIndex}

            {@const isSelected = selected.includes(cardIndex)}
            {@const isSelectedOrMatched = selected.includes(cardIndex) || matches.includes(card)}
            {@const isMatched = matches.includes(card)}

            <button class="card" 
                class:selected={isSelected}
                disabled={isSelectedOrMatched}
                on:click={() => selectCard(cardIndex)}
            >
                <div class:isMatched>{ card }</div>
            </button>
        {/each}
    </div>
{/if}

{#if state === 'lost'}
    <h1>You lost! 💩</h1>
    <button on:click={() => state = 'playing'}>Play Again</button>
{/if}

{#if state === 'won'}
    <h1>You win! 🎉</h1>
    <button on:click={() => state = 'playing'}>Play Again</button>
{/if}

<style>
    .cards {
        display: grid;
        grid-template-columns: repeat(5, 1fr);
        gap: 0.4rem;
    }

    .card {
        height: 10rem;
        width: 10rem;
        font-size: 5rem;
        background-color: var(--bg-2);

        &.selected {
            border: 4px solid var(--border);
        }

        & .isMatched {
            opacity: 0.4;
            transition: opacity 0.3s ease-out;
        }
    }

    .matches {
        display: flex;
        gap: 1rem;
        font-size: 3rem;
        margin-block: 2rem;
    }
</style>
