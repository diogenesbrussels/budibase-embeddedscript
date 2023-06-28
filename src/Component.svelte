<script>
  import { getContext } from "svelte";
  import { onMount } from "svelte";
  import { processString } from "@budibase/string-templates";

  export let collection, table, contextObject;

  const { styleable, API } = getContext("sdk");
  const component = getContext("component");
  const { builderStore } = getContext("sdk");
  const inBuilder = $builderStore.inBuilder;

  onMount(async () => {
    try {
      const scriptContainer = document.getElementById("scriptContainer");

      const scriptCollection = await API.post({
        url: `/api/` + table.tableId + `/search`,
        body: { query: { equal: { "1:collection": collection } } },
      });

      const combinedContext =
        typeof contextObject === "object" && contextObject !== null
          ? { ...component, ...contextObject }
          : component;

      for (const scriptRow of scriptCollection.rows) {
        let parentElement;
        let script = document.createElement("script");

        // Test if content is a valid URL and try to fetch content 
        try {
          let response = await fetch (new URL(scriptRow.content));
          if(response.ok) {
            script.innerHTML = await response.text();
          }
        } catch (_) {
          script.innerHTML = await processString(
            scriptRow.content,
            combinedContext
          );
        }

        if (inBuilder && scriptRow.inBuilder) {
          if (scriptRow.parent == "head") {
            parentElement = document.head;
          } else if (scriptRow.parent == "body") {
            parentElement = document.body;
          } else if (scriptRow.parent == "component") {
            parentElement = scriptContainer;
          }
          parentElement.appendChild(script);
        }

        if (!inBuilder) {
          if (scriptRow.parent == "head") {
            parentElement = document.head;
          } else if (scriptRow.parent == "body") {
            parentElement = document.body;
          } else if (scriptRow.parent == "component") {
            parentElement = scriptContainer;
          }
          parentElement.appendChild(script);
        }
      }
    } catch (error) {
      console.error("An error occurred:", error);
    }
  });
</script>

<div use:styleable={$component.styles}>
  {#if inBuilder}
    <div>
      📜 This component embeds your script collection here. This message is only
      visible in the builder. 📜
    </div>
  {/if}
  <div id="scriptContainer" />
</div>
