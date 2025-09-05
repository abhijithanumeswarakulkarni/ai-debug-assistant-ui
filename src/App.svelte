<script>
  import { API_BASE_URL } from "./constants/api";
  import { marked } from "marked";

  let errorLog = "";
  let response = "";
  let responseHtml = "";
  let loading = false;
  let responseContainer;

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

      // Handle both old shape ({response: "json string"}) and new shape ({explanation: ...})
      const raw = data?.response ?? data;
      let parsed = null;

      if (typeof raw === "string") {
        try {
          parsed = JSON.parse(raw);
        } catch {
          parsed = null;
        }
      } else if (raw && typeof raw === "object") {
        parsed = raw;
      }

      if (!parsed || typeof parsed !== "object") {
        response = "Invalid Response.";
        responseHtml = await marked.parse(response);
        return;
      }

      // Build response text
      response = `### 💡 Explanation
${parsed.explanation || "No explanation provided."}

### 🛠️ Suggested Fix
${parsed.suggested_fix || "No fix provided."}`;

      if (Array.isArray(parsed.suggested_external_links) && parsed.suggested_external_links.length) {
        response += `

### 🔗 External Links
<div class="external-links">
${parsed.suggested_external_links
  .map(link => `<a class="external-link-card" href="${link}" target="_blank" rel="noopener noreferrer">${link}</a>`)
  .join("")}
</div>`;
      }

      responseHtml = await marked.parse(response);

    } catch (err) {
      response = "Error connecting to server.";
      responseHtml = "";
    } finally {
      loading = false;
    }
  }

  $: if (responseContainer && responseHtml) {
    responseContainer.innerHTML = responseHtml;
  }
</script>

<main class="app-container">
  <h1>🤖 AI Debug Assistant</h1>

  <form on:submit|preventDefault={handleSubmit}>
    <textarea
      rows="6"
      placeholder="Paste your error message here..."
      bind:value={errorLog}
      required
      class="error-input"
    ></textarea>
    <button type="submit" disabled={loading} class="submit-button">
      {loading ? "Thinking..." : "Explain Error"}
    </button>
  </form>

  {#if response}
    <div class="response-wrapper">
      <div class="response-box" bind:this={responseContainer}></div>
    </div>
  {/if}
</main>