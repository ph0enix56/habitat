<script>
  import { onMount } from 'svelte';
  import { get, set } from 'idb-keyval';

  let testValue = $state('');
  let savedValue = $state(null);
  let isPersistent = $state(false);
  let statusMessage = $state('');

  const DB_KEY = 'test_entry';

  async function loadData() {
    const val = await get(DB_KEY);
    savedValue = val ?? 'None';
  }

  async function handleSave() {
    await set(DB_KEY, testValue);
    testValue = '';
    statusMessage = 'Saved to IndexedDB!';
    await loadData();
    setTimeout(() => (statusMessage = ''), 2000);
  }

  onMount(async () => {
    // Request persistent storage to protect against browser eviction
    if (navigator.storage && navigator.storage.persist) {
      isPersistent = await navigator.storage.persist();
    }
    await loadData();
  });
</script>

<main style="font-family: sans-serif; padding: 2rem; max-width: 480px;">
  <h2>Habit Tracker Core Test</h2>

  <section style="margin-bottom: 1.5rem;">
    <p><strong>Storage Persistent:</strong> {isPersistent ? 'Yes' : 'No / Default'}</p>
    <p><strong>Value in IndexedDB:</strong> {savedValue}</p>
  </section>

  <form onsubmit={(e) => { e.preventDefault(); handleSave(); }}>
    <label for="test-input" style="display: block; margin-bottom: 0.5rem;">Test Input:</label>
    <input 
      id="test-input"
      type="text" 
      bind:value={testValue} 
      placeholder="Type something..." 
      required
    />
    <button type="submit">Save</button>
  </form>

  {#if statusMessage}
    <p style="color: green;">{statusMessage}</p>
  {/if}
</main>
