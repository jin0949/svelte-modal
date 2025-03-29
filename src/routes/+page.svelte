<script lang="ts">
  import { modal } from "$lib/components/Modal.svelte";
  import { toast } from "$lib/components/Toast.svelte";
	import { items } from "$lib/constant";
	import type { Item } from "$lib/type";
	import ListContent from "./_components/ListContent.svelte";
	import Step1 from "./_components/Step1.svelte";
	import Step2 from "./_components/Step2.svelte";
	import Step3 from "./_components/Step3.svelte";

  const onBaseModalClick = () => {
    modal.open("title", "content \n content2")
  }
  
  const onConfirmModalClick = () => {
    modal.open("title", "content", () => {toast.info('You clicked the modal.')})
  }
  
  const onPromiseModalClick = () => {
    modal.open("title", "content", async () => {
      await new Promise<void>((resolve) => {
        setTimeout(() => {
          toast.info('You clicked the promise modal.');
          resolve();
        }, 2000);
      });
    });
  }
  
  const onContentModalClick = () => {
    const handleItemClick = (item: Item) => {
      modal.close();
      toast.info(`${item.title}이(가) 선택되었습니다.`);
    };
    modal.open(
      "title", 
      ListContent, 
      { 
        items: items, 
        onItemClick: handleItemClick
      }
    );
  }

  const step1Modal = () => {
    modal.open("step1", Step1, { onNext: step2Modal} )
  }

  const step2Modal = () => {
    modal.open("step2", Step2, { 
      onPrev: step1Modal,
      onNext: step3Modal
    })
  }

  const step3Modal = () => {
    modal.open("step3", Step3, {
      onPrev: step2Modal,
      onConfirm: () => {
        modal.close()
        toast.info('작업이 완료되었습니다.')
      }
    })
  }
</script>

<div class="min-h-screen flex flex-col justify-center items-center">
  <div class="flex flex-col gap-y-2">
    <button 
      onclick={onBaseModalClick}
      class="btn btn-primary">base modal
    </button>
    <button 
      onclick={onConfirmModalClick}
      class="btn btn-secondary">confirm modal
    </button>
    <button 
      onclick={onPromiseModalClick}
      class="btn btn-success">promise modal
    </button>
    <button 
      onclick={onContentModalClick}
      class="btn btn-error">content modal
    </button>
    <button 
      onclick={step1Modal}
      class="btn btn-warning">step modal
    </button>
  </div>
</div>
