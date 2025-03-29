<script lang="ts" module>
  import type { Component, ComponentProps } from 'svelte';

  interface ModalContentType<T extends Component<any>> {
    component: T;
    props: ComponentProps<T>;
  }

  let isOpen = $state(false);
  let title = $state('');
  let content = $state<ModalContentType<Component<any>> | string | null>(null);
  let onConfirm = $state<(() => void | Promise<void>) | null>(null);
  let isLoading = $state(false);

  function open(
    newTitle: string,
    newContent: string,
    confirmCallback?: (() => void | Promise<void>) | null
  ): boolean;

  function open<T extends Component<any>>(
    newTitle: string,
    newContent: T,
    props: ComponentProps<T>,
    confirmCallback?: (() => void | Promise<void>) | null
  ): boolean;

  function open<T extends Component<any>>(
    newTitle: string,
    newContent: string | T,
    propsOrCallback?: ComponentProps<T> | (() => void | Promise<void>) | null,
    confirmCallback?: (() => void | Promise<void>) | null
  ): boolean {
    title = newTitle;

    if (typeof newContent === 'string') {
      content = newContent;
      onConfirm = typeof propsOrCallback === 'function' ? propsOrCallback : (confirmCallback || null);
    } else {
      content = {
        component: newContent,
        props: propsOrCallback as ComponentProps<T>
      };
      onConfirm = confirmCallback || null;
    }

    isOpen = true;
    return true;
  }

  function close() {
    isOpen = false;
    setTimeout(() => {
      title = '';
      content = null;
      onConfirm = null;
      isLoading = false;
    }, 300);
  }

  async function handleConfirm() {
    if (!onConfirm) {
      close();
      return;
    }
    isLoading = true;
    try {
      const result = onConfirm();
      if (result instanceof Promise) await result;
      close();
    } catch (error) {
      console.error('작업 중 오류가 발생했습니다.', error);
    } finally {
      isLoading = false;
    }
  }

  function toggleLoading(state?: boolean) {
    isLoading = state !== undefined ? state : !isLoading;
    return isLoading;
  }

  export const modal = {
    open,
    close,
    toggleLoading,
    get isOpen() { return isOpen; },
    get isLoading() { return isLoading; },
    get currentTitle() { return title; },
    get currentContent() { return content; }
  };
</script>


<dialog class="modal" open={isOpen}>
  <div class="modal-box">
    <button
      type="button"
      class="btn btn-ghost btn-circle btn-sm absolute right-2 top-2"
      onclick={close}
    >
      ✕
    </button>

    <h3 class="font-bold text-lg">{title}</h3>

    <div class="py-4 overflow-y-auto max-h-[50vh]">
      {#if typeof content === 'string'}
        <p class="whitespace-pre-line">{content}</p>
      {:else if content && 'component' in content}
        {@const Component = content.component}
        <Component {...content.props} />
      {/if}
    </div>
    {#if onConfirm}
      <div class="modal-action">
        <button type="button" class="btn" onclick={close} disabled={isLoading}>close</button>

        <button type="button" class="btn btn-primary" onclick={handleConfirm} disabled={isLoading}>
          {#if isLoading}<span class="loading loading-spinner loading-sm"></span>{/if}
          confirm
        </button>
      </div>
    {/if}
  </div>

  <!-- svelte-ignore a11y_click_events_have_key_events -->
  <!-- svelte-ignore a11y_no_static_element_interactions -->
  <div class="modal-backdrop" onclick={close}></div>
</dialog>
