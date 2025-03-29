<script lang="ts">
  const { onPrev, onConfirm } = $props<{ 
    onPrev: () => void
    onConfirm: () => void 
  }>()
</script>

<div class="card gap-y-4">
  step2 content
  <div class="grid grid-cols-2 gap-x-2">
    <button 
      onclick={onPrev}
      class="btn btn-secondary">
      prev
    </button>
    <button 
      onclick={onConfirm}
      class="btn btn-sucess">
      confirm
    </button>
  </div>
</div>