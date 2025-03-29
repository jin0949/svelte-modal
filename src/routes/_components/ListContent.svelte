<script lang="ts">
	import type { Item } from "$lib/type";

  const { items, onItemClick } = $props<{ 
    items: Item[],
    onItemClick: (item: Item) => void
  }>()

</script>

<div class="card bg-base-100 shadow-xl">
  <ul class="menu bg-base-200 rounded-box w-full space-y-2">
    {#each items as item (item.id)}
      <li>
        <button 
          class="py-3 hover:bg-base-300 transition-colors duration-200" 
          onclick={() => onItemClick(item)}
        >
          <div class="flex flex-col items-start">
            <span class="font-medium">{item.title}</span>
            <span class="text-xs text-base-content/70">{item.description}</span>
          </div>
        </button>
      </li>
    {/each}
  </ul>
</div>
