<script lang="ts">
  import type { Movie } from '$lib/types';

  // Props con Svelte 5: sistema de tipos explícito y callbacks en lugar de eventos
  let { 
    movie,
    showActions = true,
    ondelete,
    onedit,
    onfavoritetoggle,
    onrate
  }: {
    movie: Movie;
    showActions?: boolean;
    ondelete?: (id: string) => void;
    onedit?: (movie: Movie) => void;
    onfavoritetoggle?: (id: string) => void;
    onrate?: (rating: number) => void;
  } = $props();

  // Handlers: ejecutan callbacks del padre directamente
  function handleDelete() {
    ondelete?.(movie.id);
  }

  function handleEdit() {
    onedit?.(movie);
  }

  function handleToggleFavorite() {
    onfavoritetoggle?.(movie.id);
  }

  function handleRate(rating: number) {
    onrate?.(rating);
  }
</script>

<!-- Componente reutilizable: tarjeta para mostrar información de una película -->
<article class="flex flex-col overflow-hidden rounded-lg border border-slate-200 bg-white shadow-sm">
  {#if movie.posterUrl}
    <div class="flex h-48 items-center justify-center bg-slate-100">
      <img
        alt={`Póster de ${movie.title}`}
        class="max-h-full max-w-full object-contain"
        src={movie.posterUrl}
        loading="lazy"
      />
    </div>
  {/if}

  <div class="flex flex-1 flex-col gap-3 p-4">
    <header>
      <h3 class="text-lg font-semibold text-slate-900">{movie.title}</h3>
      <p class="text-sm text-slate-600">Dirigida por {movie.director}</p>
    </header>

    <div class="mt-auto text-sm text-slate-500">
      {#if movie.year}
        <span>Año: {movie.year}</span>
      {/if}
    </div>

    <!-- Sistema de calificación con 5 estrellas -->
    <div class="flex items-center gap-1">
      {#each { length: 5 } as _, i}
        <button
          type="button"
          class="text-2xl transition-transform hover:scale-110"
          title="Calificar: {i + 1} estrellas"
          onclick={() => handleRate(i + 1)}
        >
          {i + 1 <= (movie.rating ?? 0) ? '⭐' : '☆'}
        </button>
      {/each}
      {#if movie.rating && movie.rating > 0}
        <span class="ml-2 text-sm text-slate-600">({movie.rating}/5)</span>
      {:else}
        <span class="ml-2 text-sm text-slate-400">Sin calificar</span>
      {/if}
    </div>

    {#if showActions}
      <div class="mt-3 flex flex-col gap-2 sm:flex-row">
        <button
          type="button"
          class="rounded border px-3 py-2 transition {movie.isFavorite ? 'border-yellow-500 bg-yellow-50 text-yellow-600 hover:bg-yellow-100' : 'border-slate-300 text-slate-700 hover:bg-slate-50'}"
          title={movie.isFavorite ? 'Quitar de favoritos' : 'Agregar a favoritos'}
          onclick={handleToggleFavorite}
        >
          {movie.isFavorite ? '⭐ Favorito' : '☆ Favorito'}
        </button>
        <button
          type="button"
          class="w-full rounded border border-slate-300 px-3 py-2 text-slate-700 transition hover:bg-slate-50"
          onclick={handleEdit}
        >
          Editar
        </button>
        <button
          type="button"
          class="w-full rounded border border-red-500 px-3 py-2 text-red-600 transition hover:bg-red-50"
          onclick={handleDelete}
        >
          Eliminar
        </button>
      </div>
    {/if}
  </div>
</article>
