<script lang="ts">
  const { onNext } = $props<{ onNext: () => void }>()
</script>

<div class="card gap-y-4">
  step1 content
  <button 
    onclick={onNext}
    class="btn btn-primary">
    next
  </button>
</div>