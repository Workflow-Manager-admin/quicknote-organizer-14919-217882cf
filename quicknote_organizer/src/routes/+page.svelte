<script>
  // PUBLIC_INTERFACE
  /** QuickNote Organizer main container component.
   * No backend logic – all state is local. Skeleton for note CRUD, search, filter, modal edit. 
   */
  // All color theme values are now in CSS, JS COLORS removed.

  // Mock category/tag list
  let categories = ['All', 'Work', 'Personal', 'Ideas'];

  // State
  let search = "";
  let selectedCategory = 'All';
  let notes = [
    {
      id: 1,
      title: "Welcome to QuickNote",
      content: "This is a sample note. Click to edit!",
      category: "Personal"
    },
    {
      id: 2,
      title: "Organize your thoughts",
      content: "Use categories to keep notes manageable.",
      category: "Work"
    }
  ];
  let showModal = false;
  let modalNote = null; // note currently being viewed/edited; null for new
  let isEdit = false;

  // PUBLIC_INTERFACE
  function openNewNoteModal() {
    modalNote = { title: "", content: "", category: categories[1] || "" };
    isEdit = false;
    showModal = true;
  }

  // PUBLIC_INTERFACE
  function openEditNoteModal(note) {
    modalNote = { ...note };
    isEdit = true;
    showModal = true;
  }

  // PUBLIC_INTERFACE
  function closeModal() {
    showModal = false;
    modalNote = null;
    isEdit = false;
  }

  // PUBLIC_INTERFACE
  function saveNote() {
    if (isEdit && modalNote.id) {
      notes = notes.map((n) => n.id === modalNote.id ? { ...modalNote } : n);
    } else {
      modalNote.id = Date.now();
      notes = [ { ...modalNote }, ...notes ];
    }
    closeModal();
  }

  // PUBLIC_INTERFACE
  function deleteNote(noteId) {
    notes = notes.filter(n => n.id !== noteId);
    closeModal();
  }

  // PUBLIC_INTERFACE
  function filteredNotes() {
    return notes.filter(n =>
      (selectedCategory === 'All' || n.category === selectedCategory) &&
      (n.title.toLowerCase().includes(search.toLowerCase()) || n.content.toLowerCase().includes(search.toLowerCase()))
    );
  }
</script>

<style>
  :global(html, body) {
    background: {COLORS.secondary};
    color: {COLORS.text};
    font-family: 'Segoe UI', Arial, sans-serif;
    margin: 0;
    padding: 0;
    min-height: 100vh;
  }

  .quicknote-container {
    max-width: 600px;
    margin: 2rem auto 4rem auto;
    background: {COLORS.secondary};
    border-radius: 10px;
    box-shadow: 0 2px 12px {COLORS.shadow};
    padding: 2rem 1.5rem 3rem 1.5rem;
    position: relative;
    min-height: 500px;
  }

  .search-bar {
    display: flex;
    gap: 0.5rem;
    margin-bottom: 1.25rem;
  }
  .search-bar input {
    flex: 1;
    padding: 0.7rem 1rem;
    border-radius: 5px;
    border: 1px solid {COLORS.border};
    font-size: 1rem;
    background: {COLORS.secondary};
    color: {COLORS.text};
    outline: none;
    transition: border 0.2s;
  }
  .search-bar input:focus {
    border: 1.5px solid {COLORS.primary};
  }

  .category-filters {
    display: flex;
    gap: 0.5rem;
    margin-bottom: 1.5rem;
    flex-wrap: wrap;
  }
  .category-btn {
    border: none;
    background: {COLORS.secondary};
    color: {COLORS.text};
    padding: 0.4rem 0.9rem;
    border-radius: 5px;
    border: 1.5px solid {COLORS.border};
    cursor: pointer;
    font-size: 0.97rem;
    transition: background 0.18s, border 0.18s;
  }
  .category-btn.selected,
  .category-btn:hover {
    background: {COLORS.primary};
    color: #fff;
    border-color: {COLORS.primary};
  }

  .notes-list {
    display: flex;
    flex-wrap: wrap;
    gap: 1rem;
    min-height: 180px;
  }
  .note-card {
    background: #fff;
    border: 1px solid {COLORS.border};
    border-radius: 8px;
    box-shadow: 0 1px 4px {COLORS.shadow};
    padding: 1rem 1.1rem 0.9rem 1.1rem;
    width: calc(50% - 0.5rem);
    min-width: 210px;
    cursor: pointer;
    transition: box-shadow 0.18s, border-color 0.18s;
    margin-bottom: 0.4rem;
    display: flex;
    flex-direction: column;
    justify-content: space-between;
  }
  .note-card:hover {
    border-color: {COLORS.accent};
    box-shadow: 0 4px 12px {COLORS.shadow};
  }
  .note-title {
    font-weight: 600;
    font-size: 1.02rem;
    margin-bottom: 0.3rem;
    color: {COLORS.primary};
  }
  .note-snippet {
    font-size: 0.93rem;
    color: #333;
    margin-bottom: 0.3rem;
    word-break: break-word;
  }
  .note-meta {
    font-size: 0.82rem;
    color: {COLORS.accent};
    margin-top: auto;
    margin-bottom: 0.3rem;
  }

  /* Floating action button */
  .fab {
    position: fixed;
    bottom: 2.4rem;
    right: 2.4rem;
    background: {COLORS.accent};
    color: #fff;
    border: none;
    border-radius: 50%;
    width: 62px;
    height: 62px;
    box-shadow: 0 2px 10px {COLORS.primary}33;
    font-size: 2.15rem;
    cursor: pointer;
    display: flex;
    align-items: center;
    justify-content: center;
    z-index: 99;
    transition: background 0.14s, box-shadow 0.18s;
  }
  .fab:hover {
    background: {COLORS.primary};
    box-shadow: 0 4px 16px {COLORS.shadow};
  }

  /* Modal styling */
  .modal-backdrop {
    position: fixed;
    top: 0; left: 0; bottom: 0; right: 0;
    background: rgba(0,0,0,0.13);
    z-index: 100;
    display: flex;
    align-items: center;
    justify-content: center;
    transition: background 0.2s;
  }
  .modal-card {
    background: #fff;
    border-radius: 9px;
    box-shadow: 0 8px 32px {COLORS.shadow};
    max-width: 420px;
    width: 94vw;
    padding: 2.2rem 1.6rem 1.5rem 1.6rem;
    display: flex;
    flex-direction: column;
    position: relative;
    animation: modalIn 0.14s;
  }
  @keyframes modalIn {
    from { transform: translateY(42px) scale(0.98); opacity: 0.6; }
    to   { transform: none; opacity: 1; }
  }
  .modal-card label {
    font-weight: 500;
    margin-bottom: 2px;
    color: {COLORS.primary};
    font-size: 0.98rem;
    margin-top: 1.1rem;
  }
  .modal-card input, .modal-card textarea, .modal-card select {
    border: 1.3px solid {COLORS.border};
    border-radius: 6px;
    padding: 0.64rem 0.9rem;
    width: 100%;
    font-size: 1rem;
    margin-top: 0.15rem;
    background: #fff;
    color: {COLORS.text};
    margin-bottom: 0.7rem;
    resize: none;
    outline: none;
    transition: border 0.2s;
  }
  .modal-card input:focus, .modal-card textarea:focus, .modal-card select:focus {
    border-color: {COLORS.primary};
  }
  .modal-actions {
    display: flex;
    gap: 0.9rem;
    margin-top: 1.2rem;
    justify-content: flex-end;
  }
  .btn {
    border: none;
    border-radius: 5px;
    padding: 0.6rem 1.4rem;
    font-size: 1rem;
    cursor: pointer;
    font-weight: 500;
    background: {COLORS.primary};
    color: #fff;
    transition: background 0.16s, color 0.14s, box-shadow 0.18s;
    box-shadow: 0 1px 4px {COLORS.shadow};
  }
  .btn.accent {
    background: {COLORS.accent};
    color: #fff;
  }
  .btn.delete {
    background: #fff;
    border: 1.5px solid {COLORS.accent};
    color: {COLORS.accent};
  }
  .btn.cancel {
    background: #fff;
    color: {COLORS.primary};
    border: 1.5px solid {COLORS.primary};
  }
  .btn:hover {
    opacity: 0.88;
    box-shadow: 0 2px 12px {COLORS.shadow};
  }
</style>

<div class="quicknote-container">
  <!-- Search Bar -->
  <form class="search-bar" on:submit|preventDefault>
    <input
      type="search"
      placeholder="Search notes..."
      bind:value={search}
      aria-label="Search notes"
    >
  </form>

  <!-- Category Filters -->
  <div class="category-filters">
    {#each categories as cat}
      <button
        class="category-btn {selectedCategory === cat ? 'selected' : ''}"
        type="button"
        aria-pressed={selectedCategory === cat}
        on:click={() => selectedCategory = cat}
      >{cat}</button>
    {/each}
  </div>

  <!-- Notes List -->
  <div class="notes-list">
    {#if filteredNotes().length === 0}
      <span style="opacity:0.7;padding:2.3rem;font-size:1.1rem;">No notes found.</span>
    {/if}
    {#each filteredNotes() as note (note.id)}
      <div
        class="note-card"
        on:click={() => openEditNoteModal(note)}
        tabindex="0"
        aria-label={`Open note: ${note.title}`}
      >
        <div class="note-title">{note.title}</div>
        <div class="note-snippet">{note.content.length > 74 ? note.content.slice(0,74) + '…' : note.content}</div>
        <div class="note-meta">{note.category}</div>
      </div>
    {/each}
  </div>
</div>

<!-- Floating Action Button (FAB) to add note -->
<button class="fab" title="Add new note" aria-label="Add new note" on:click={openNewNoteModal}>+</button>

<!-- Modal for add/edit note -->
{#if showModal}
  <div class="modal-backdrop" on:click={closeModal}>
    <div class="modal-card" on:click|stopPropagation>
      <h2 style="margin-top:0;">{isEdit ? "Edit note" : "New note"}</h2>

      <label for="note-title">Title</label>
      <input
        id="note-title"
        type="text"
        placeholder="Title"
        bind:value={modalNote.title}
        maxlength="60"
        autofocus
      >

      <label for="note-content">Content</label>
      <textarea
        id="note-content"
        placeholder="Write your note here..."
        bind:value={modalNote.content}
        rows="6"
        maxlength="2000"
        style="font-size:1rem;"
      ></textarea>

      <label for="note-category">Category</label>
      <select
        id="note-category"
        bind:value={modalNote.category}
      >
        {#each categories.filter(c => c !== 'All') as cat}
          <option value={cat}>{cat}</option>
        {/each}
      </select>

      <div class="modal-actions">
        <button class="btn" on:click|preventDefault={saveNote}>{isEdit ? "Save" : "Add"}</button>
        {#if isEdit}
          <button class="btn delete" on:click|preventDefault={() => deleteNote(modalNote.id)}>Delete</button>
        {/if}
        <button class="btn cancel" on:click|preventDefault={closeModal}>Cancel</button>
      </div>
    </div>
  </div>
{/if}
