<template>
  <div class="container">
    <div id="autocomplete" />
  </div>
</template>

<script lang="jsx">
import { Fragment, render, onMounted } from "vue";
import { autocomplete } from "@algolia/autocomplete-js";
import { cities } from "./cities";

import "@algolia/autocomplete-theme-classic";

import { createElement } from "./utils/createElement";
import ProductItem from "./components/ProductItem.vue";

export default {
  name: "App",
  data() {
    return {
      inputValue: "ex: 21009, Bekkouche Lakhder, skikda",
    };
  },
  setup() {
    onMounted(() => {
      autocomplete({
        container: "#autocomplete",
        placeholder: "ex: 21009, Bekkouche Lakhder, skikda",
        getSources({ query, setQuery, refresh }) {
          return [
            {
              onSelect({ item }) {
                document.querySelector(
                  ".aa-DetachedSearchButtonPlaceholder"
                ).innerText = `${item.code}, ${item.title}, ${item.region}`;
                this.inputValue = `${item.code}, ${item.title}, ${item.region}`;
                document.querySelector(
                  ".aa-Input"
                ).value = `${item.code}, ${item.title}, ${item.region}`;
                setQuery(`${item.code}, ${item.title}, ${item.region}`);
              },
              sourceId: "products",
              getItemInputValue: ({ item }) => {
                console.log(item);
                return `${item.code}, ${item.title}, ${item.region}`;
              },
              getItems() {
                return new Promise((resolve) => setTimeout(resolve, 1000)).then(
                  () => {
                    return cities.filter(
                      (city) =>
                        city.title
                          .toLowerCase()
                          .includes(query.toLowerCase()) ||
                        city.region.toLowerCase().includes(query.toLowerCase())
                    );
                  }
                );
              },
              templates: {
                item({ item, components }) {
                  // console.log(item);
                  return (
                    <li>
                      {item.code}, {item.title}, {item.region}
                    </li>
                    // <ProductItem item={item} highlight={components.Highlight} />
                  );
                },
              },
            },
          ];
        },
        renderer: {
          createElement,
          Fragment,
          render,
        },
        // onStateChange({ state }) {
        //   console.log(state);
        // },
      });
    });
  },
};
</script>

<style>
.container {
  margin: 0 auto;
  max-width: 640px;
  width: 100%;
}

#autocomplete .aa-InputWrapperPrefix,
.aa-DetachedOverlay .aa-InputWrapperSuffix {
  display: none;
}

#autocomplete .aa-DetachedSearchButton,
#autocomplete .aa-Form,
.aa-DetachedOverlay .aa-Form {
  background-color: #f5f8fa;
  border-color: #f5f8fa;
  transition: color 0.2s ease, background-color 0.2s ease;
  appearance: none;
  background-clip: padding-box;
  border-radius: 0.65rem;
  color: #5e6278;
  display: block;
  font-size: 1.1rem;
  font-weight: 500;
  line-height: 1.5;
  padding: 9.75px 13px;
  width: 100%;
  line-height: inherit;
  margin: 0;
  box-shadow: none;
}

.aa-DetachedOverlay .aa-Form:focus-within {
  background-color: #eef3f7;
  border-color: #eef3f7;
  color: #5e6278;
  transition: color 0.2s ease, background-color 0.2s ease;
  outline: none;
  box-shadow: none;
}

#autocomplete .aa-DetachedSearchButtonIcon,
#autocomplete .aa-InputWrapperSuffix,
.aa-DetachedOverlay .aa-InputWrapperPrefix {
  display: none;
}
</style>
