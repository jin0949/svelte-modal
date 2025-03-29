<script lang="ts">
  const { onPrev, onNext } = $props<{ 
    onPrev: () => void
    onNext: () => void 
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
      onclick={onNext}
      class="btn btn-primary">
      next
    </button>
  </div>
</div>