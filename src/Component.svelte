<script>
  import { getContext } from "svelte";
  import { onMount } from "svelte";
  import { processStringSync } from "@budibase/string-templates";

  export let collection, table;

  const { styleable, API } = getContext("sdk");
  const component = getContext("component");
  const { builderStore } = getContext("sdk");
  const inBuilder = $builderStore.inBuilder;

  onMount(async () => {
    let scriptContainer = document.getElementById("scriptContainer");

    let scriptCollection = await API.post({
      url: `/api/` + table.tableId + `/search`,
      body: { query: { equal: { "1:collection": collection } } },
    });

    for (const scriptRow of scriptCollection.rows) {
      let parentElement;
      let script = document.createElement("script");
      script.innerHTML = processStringSync(scriptRow.content, component);

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
