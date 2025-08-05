<script>
  import { API_BASE_URL } from "./constants/api";
  import { marked } from "marked";

  let errorLog = "";
  let response = "";
  let responseHtml = "";
  let loading = false;

  async function handleSubmit() {
    loading = true;
    response = "";
    responseHtml = "";

    try {
      const res = await fetch(`${API_BASE_URL}/api/explain`, {
        method: "POST",
        headers: { "Content-Type": "application/json" },
        body: JSON.stringify({ error_log: errorLog }),
      });

      const data = await res.json();
      response = data.response || 'No explanation returned.';
      responseHtml = await marked.parse(response);
    } catch (err) {
      response = "Error connecting to server.";
      responseHtml = "";
    } finally {
      loading = false;
    }
  }

  const PING_INTERVAL_MS = 10 * 60 * 1000;
  setInterval(() => {
    fetch(`${API_BASE_URL}/ping`)
      .then(() => console.log("Pinged backend to keep it warm"))
      .catch(() => console.warn("Ping failed (backend may be down)"));
  }, PING_INTERVAL_MS);
</script>

<main
  style="font-family: sans-serif; padding: 2rem; max-width: 800px; margin: auto;"
>
  <h1>🛠️ AI Debug Assistant</h1>

  <form on:submit|preventDefault={handleSubmit}>
    <textarea
      rows="6"
      placeholder="Paste your error message here..."
      bind:value={errorLog}
      required
      style="width: 100%; margin-bottom: 1rem;"
    ></textarea>
    <button type="submit" disabled={loading}>
      {loading ? "Thinking..." : "Explain Error"}
    </button>
  </form>

  {#if response}
    <div style="margin-top: 2rem; white-space: pre-wrap;">
      <h3>💡 Explanation & Fix:</h3>
      <p>{response}</p>
      <button on:click={() => navigator.clipboard.writeText(response)}>
        📋 Copy
      </button>
    </div>
  {/if}
</main>
