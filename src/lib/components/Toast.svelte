<script lang="ts" module>
  import { fade, fly } from "svelte/transition"

  export const TOAST_CONFIG = {
    duration: 3000,
    colors: {
      info: "#3b82f6",
      success: "#22c55e",
      error: "#ef4444",
      warning: "#f59e0b"
    },
    icons: {
      info: `<svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24"><path fill="currentColor" d="M11 17h2v-6h-2v6Zm1-8q.425 0 .713-.288T13 8t-.288-.713T12 7t-.713.288T11 8t.288.713T12 9Zm0 13q-2.075 0-3.9-.788t-3.175-2.137t-2.138-3.175T2 12t.788-3.9t2.137-3.175t3.175-2.138T12 2t3.9.788t3.175 2.137t2.138 3.175T22 12t-.788 3.9t-2.137 3.175t-3.175 2.138T12 22Zm0-2q3.35 0 5.675-2.325T20 12t-2.325-5.675T12 4T6.325 6.325T4 12t2.325 5.675T12 20Zm0-8Z"/></svg>`,
      success: `<svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24"><path fill="currentColor" d="M12 2C6.48 2 2 6.48 2 12s4.48 10 10 10s10-4.48 10-10S17.52 2 12 2zm0 18c-4.41 0-8-3.59-8-8s3.59-8 8-8s8 3.59 8 8s-3.59 8-8 8zm4.59-12.42L10 14.17l-2.59-2.58L6 13l4 4l8-8z"/></svg>`,
      error: `<svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24"><path fill="currentColor" d="M11 15h2v2h-2v-2zm0-8h2v6h-2V7zm.99-5C6.47 2 2 6.48 2 12s4.47 10 9.99 10C17.52 22 22 17.52 22 12S17.52 2 11.99 2zM12 20c-4.42 0-8-3.58-8-8s3.58-8 8-8s8 3.58 8 8s-3.58 8-8 8z"/></svg>`,
      warning: `<svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24"><path fill="currentColor" d="M12 5.99L19.53 19H4.47L12 5.99M12 2L1 21h22L12 2zm1 14h-2v2h2v-2zm0-6h-2v4h2v-4z"/></svg>`
    }
  } as const

  type ToastType = keyof typeof TOAST_CONFIG.colors;
  type ToastPosition = "top" | "bottom";

  interface ToastMessage {
    id: string;
    message: string;
    type: ToastType;
    position: ToastPosition;
  }

  let toasts = $state<ToastMessage[]>([]);
  const derivedToasts = $derived(toasts);

  const createToast = (message: string, type: ToastType, position: ToastPosition = "top") => {
    const toast: ToastMessage = {
      id: crypto.randomUUID(),
      message,
      type,
      position
    };
    
    toasts = [...toasts, toast];
    
    setTimeout(() => {
      removeToast(toast.id);
    }, TOAST_CONFIG.duration);
  };

  const removeToast = (id: string) => {
    toasts = toasts.filter(t => t.id !== id);
  };

  export const toast = {
    info: (message: string, { position = "top" }: { position?: ToastPosition } = {}) => 
      createToast(message, "info", position),
    
    success: (message: string, { position = "top" }: { position?: ToastPosition } = {}) => 
      createToast(message, "success", position),
    
    error: (message: string, { position = "top" }: { position?: ToastPosition } = {}) => 
      createToast(message, "error", position),
    
    warning: (message: string, { position = "top" }: { position?: ToastPosition } = {}) => 
      createToast(message, "warning", position)
  };
</script>

{#each ['top', 'bottom'] as position}
  <div class="fixed w-full px-4 z-50 flex flex-col items-center pointer-events-none {position === 'top' ? 'top-12' : 'bottom-12 mb-4'}">
    {#each derivedToasts.filter(t => t.position === position).reverse() as toast, i (toast.id)}
      <div
        class="flex items-center w-full max-w-4xl mx-auto px-8 py-4 rounded pointer-events-auto transform-gpu text-white"
        role="alert"
        style="
          opacity: 100%;
          background-color: {TOAST_CONFIG.colors[toast.type]};
          transform: translateY({4 * i}px);
          z-index: {999 - i};
        "
        in:fly={{ y: position === 'top' ? -100 : 100, duration: 300 }}
        out:fade={{ duration: 200 }}
      >
        <div class="w-5 h-5 flex-shrink-0">{@html TOAST_CONFIG.icons[toast.type]}</div>
        <span class="flex-1 text-sm font-medium ml-2">{toast.message}</span>
        <!-- svelte-ignore a11y_consider_explicit_label -->
        <button
          class="p-1 rounded-full hover:bg-white/20 transition-colors"
          onclick={() => removeToast(toast.id)}
        >
          <div class="w-4 h-4">
            <svg xmlns="http://www.w3.org/2000/svg" width="100%" height="100%" viewBox="0 0 24 24">
              <path fill="currentColor" d="M19 6.41L17.59 5L12 10.59L6.41 5L5 6.41L10.59 12L5 17.59L6.41 19L12 13.41L17.59 19L19 17.59L13.41 12z"/>
            </svg>
          </div>
        </button>
      </div>
    {/each}
  </div>
{/each}
