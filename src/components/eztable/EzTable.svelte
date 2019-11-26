<script>
  import { onMount, onDestroy, beforeUpdate, afterUpdate } from "svelte";
  import throttle from "lodash/throttle";
  export let bottomMarge = "10";

  let winHeight = document.documentElement.clientHeight;
  let winWidth = document.documentElement.clientWidth;
  let bottom = 0;

  let thead;
  let tbody;

  let tdList = [];
  let thList = [];

  onMount(() => {
    thead = document.getElementById("thead");
    tbody = document.getElementById("tbody");
    // Add evt observer for resize and simulate it once
    window.addEventListener("resize", handleWindowResize, { passive: true });
    // - Thead can include tr or not
    tdList = tbody.children[0].children[0].children[0].children;
    thList = thead.children[0];
    if (thList.children[0].nodeName === "TR") {
      thList = thList.children[0];
    }
    thList = thList.children;

    let index = -1;
    for (let th of thList) {
      index++;
      let style = window.getComputedStyle(th, null);
      tdList[index].style.width = style.width
      tdList[index].style.maxWidth = style.maxWidth
      tdList[index].style.minWidth = style.minWidth
    }

    refreshSize();
  });

  onDestroy(() => {
    window.removeEventListener("resize", handleWindowResize);
  });

  afterUpdate(() => {
    refreshSize();
  });

  function handleWindowResize(event) {
    if (
      winHeight !== event.currentTarget.innerHeight ||
      winWidth !== event.currentTarget.innerWidth
    ) {
      winHeight = event.currentTarget.innerHeight;
      winWidth = event.currentTarget.innerWidth;
      refreshSize();
    }
  }

  const refreshSize = throttle(function() {
    let lastElm = {};

    // Manage height
    function getBottom(elt) {
      let parent = elt.offsetParent;
      if (parent === null) {
        return 0;
      } else {
        return elt.offsetTop + getBottom(parent);
      }
    }

    bottom = getBottom(tbody) + parseInt(bottomMarge);
    tbody.style.maxHeight = winHeight - bottom + "px";

    // Manage width
    // - If tbody is empty, do nothing
    if (tbody === undefined) return; // There is no table body
    if (tbody.children.length === 0) return; // There is no row
    if (tbody.children[0].children.length === 0) return; // The first row is empty

    let marge = 0;
    let index = -1;
    for (let node of thList) {
      index++;
      if (node.nodeName === "TH") {
        let style = window.getComputedStyle(node, null);
        if (
          parseInt(
            parseFloat(style.getPropertyValue("width") || 0) - node.offsetWidth
          ) !== 0
        ) {
          marge =
            parseFloat(style.getPropertyValue("padding-left") || 0) +
            parseFloat(style.getPropertyValue("padding-right") || 0) +
            parseFloat(style.getPropertyValue("border-left-width") || 0) +
            parseFloat(style.getPropertyValue("border-right-width") || 0);
        }

        let width = 0;
        if (tdList[index] !== undefined) {
          width = tdList[index].offsetWidth - marge;
          if (node.offsetWidth > width) {
            //width = node.offsetWidth
          }
        }

        //tdList[index].style.setProperty("width", `${width}px`);
        node.style.setProperty("width", `${width}px`);
        node.style.setProperty("max-width", `${width}px`);

        lastElm = {
          elm: node,
          index: index,
          marge: marge
        };
      }
    }

    lastElm.elm.style.setProperty("width", "");
    lastElm.elm.style.setProperty(
      "max-width",
      thList[lastElm.index].offsetWidth - lastElm.marge + "px"
    );
  }, 200);
</script>

<style>
  .fill {
    width: 100%;
    width: stretch;
    width: -webkit-fill-available;
    width: -moz-available;
  }
</style>

<div style="border-bottom: 2px solid gainsboro;margin-bottom: 5px;">
  <table
    class="fill {$$props.class}"
    id = "thead"
    style="margin-bottom:0px">
    <slot name="thead" />
  </table>
  <div id="tbody" style="overflow-y:auto">
    <table class="fill {$$props.class}">
      <slot name="tbody" />
    </table>
  </div>
</div>
