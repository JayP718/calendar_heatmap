<script>
  import { getContext, onDestroy, onMount, afterUpdate, tick } from "svelte";
  import CalHeatmap from "cal-heatmap";
  import Legend from "cal-heatmap";
  import Tooltip from "cal-heatmap";

  import "./lib/cal-heatmap.css";

  export let dataProvider;
  export let dateField;
  export let valueField;

  const { API,styleable } = getContext("sdk");
  const component = getContext("component");
  
  function uuid() {
    return "cxxxxxxxxxxxx4xxxyxxxxxxxxxxxxxxx".replace(/[xy]/g, (c) => {
      const r = (Math.random() * 16) | 0;
      const v = c === "x" ? r : (r & 0x3) | 0x8;
      return v.toString(16);
    });
  }
  let component_id = uuid();


  
  $: rows_data = dataProvider?.rows ?? [];

  const cal = new CalHeatmap();

  const paint_cal = (component) => {
    console.log("painting");
    let domainArray = [];
    let max_num = Math.max(...rows_data.map((o) => o[valueField]));
    let min_num = Math.min(...rows_data.map((o) => o[valueField]));

    var i = min_num;
    let incr = (max_num - min_num) / 6;
    while (i <= max_num) {
      i = i + incr;
      domainArray.push(Math.floor(i));
    }
    console.log("#" + component_id);
    cal.paint({
      itemSelector: "#" + component_id,
      data: {
        source: rows_data,
        x: (datum) => {
          return +new Date(datum[dateField]);
        },
        y: valueField,
      },
      range: 9,
      domain: {
        type: "month",
        gutter: 5,
        dynamicDimension: true,
      },
      subDomain: {
        type: "day",
      },
      date: {
        start: new Date("2022-05-08"),
      },
      scale: {
        as: "color",
        type: "threshold",
        domain: domainArray,
        scheme: "YlOrBr",
      },
    });
  };

  onMount(async () => {
    paint_cal(component);
  });

  onDestroy(() => {
    cal.destroy().then(() => {
      console.log("Destroy complete!");
    });
  });


</script>

<div use:styleable={$component.styles}>
  <div id={component_id} />
</div>

<style>
</style>
