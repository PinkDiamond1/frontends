<script lang="ts">
export default {
  name: "PageResolver",
};
</script>

<script setup lang="ts">
import { Ref, resolveComponent } from "vue";
import { pascalCase } from "scule";
import {
  useNavigationContext,
  useNavigationSearch,
} from "@shopware-pwa/composables-next";
import { SeoUrl } from "@shopware-pwa/types";

const { resolvePath } = useNavigationSearch();
const route = useRoute();

const { data: seoResult } = await useAsyncData(
  "cmsResponse" + route.path,
  async () => {
    const seoUrl = await resolvePath(route.path);
    return seoUrl;
  }
);
const { routeName, foreignKey } = useNavigationContext(
  seoResult as Ref<SeoUrl>
);

function render() {
  const componentName = routeName.value;
  const componentNameToResolve = pascalCase(componentName as string);
  const cmsPageView = routeName && resolveComponent(componentNameToResolve);
  if (cmsPageView) {
    if (cmsPageView === componentNameToResolve)
      return h("div", {}, "Problem resolving component: " + componentName);
    return h("div", h(cmsPageView, { navigationId: foreignKey.value }));
  }
  return h("div", {}, "Loading...");
}
</script>

<template>
  <render />
</template>
