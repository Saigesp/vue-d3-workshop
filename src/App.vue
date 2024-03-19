<template>
  <div class="chart__wrapper">
    <div ref="chart" id="chart" class="chart"/>
  </div>
</template>

<script setup>
const data = [
  { name: 'rosalia', ventas: 13546384, age: 25 },
  { name: 'manuchao', ventas: 3843458, age: 42 },
  { name: 'bad bunny', ventas: 48641384, age: 28 },
  { name: 'shakira', ventas: 68174616, age: 44 },
  { name: 'jbalvin', ventas: 35435354, age: 31 },
  { name: 'daddy yankee', ventas: 32434135, age: 39 },
  { name: 'maluma', ventas: 35413533, age: 27 },
  { name: 'ozuna', ventas: 3413541, age: 33 },
  { name: 'anuel aa', ventas: 9615521, age: 29 },
  { name: 'karol g', ventas: 3432543, age: 26 },
];

import { onMounted, ref } from 'vue';
import { select } from 'd3-selection';
import { scaleLinear, scaleBand, scaleSequential } from 'd3-scale';
import { interpolateViridis } from 'd3-scale-chromatic';
import { axisLeft, axisBottom } from 'd3-axis';
import { transition } from 'd3-transition';
import { easeBackInOut } from 'd3-ease';

const chart = ref(null);
const svg = ref();
const group = ref();
const axisL = ref();
const axisB = ref();

const margin = 40;
const width = 660;
const height = 260;

const ventasScale = scaleLinear()
  .domain([0, Math.max(...data.map((d) => d.ventas))])
  .range([height, 0]);

const bandScale = scaleBand()
  .range([0, width])
  .domain(data.map((d) => d.name))
  .padding(0.1);

const colorScale = scaleSequential(interpolateViridis)
  .domain([18, Math.max(...data.map((d) => d.age))]);

const axisConfig = axisLeft(ventasScale).tickSize(-width).ticks(5, "s")

const constructChart = () => {
  svg.value = select(chart.value)
    .append('svg')
    .attr('width', width + margin + margin)
    .attr('height', height + margin + margin);

  group.value = svg.value.append('g')
    .attr('transform', `translate(${margin}, ${margin})`);

  axisL.value = group.value.append("g")
    .attr("class", "axis")
    .call(axisConfig);

  axisB.value = group.value.append("g")
    .attr("class", "axis")
    .attr("transform", `translate(0, ${height})`)
    .call(axisBottom(bandScale));

  group.value.selectAll('rect')
    .data(data, (d) => d.name)
    .enter()
    .append('rect')
    .attr('x', (d) => bandScale(d.name))
    .attr('y', (d) => ventasScale(d.ventas))
    .attr('width', bandScale.bandwidth())
    .attr('height', (d) => height - ventasScale(d.ventas))
    .attr('fill', (d) => colorScale(d.age));
}

const updateChart = () => {
  ventasScale.domain([0, Math.max(...data.map((d) => d.ventas))])
  bandScale.domain(data.map((d) => d.name))

  axisL.value
    .transition()
    .ease(easeBackInOut)
    .duration(3000)
    .call(axisConfig);

  axisB.value
    .transition()
    .ease(easeBackInOut)
    .duration(3000)
    .call(axisBottom(bandScale));

  group.value.selectAll('rect');

  group.value.selectAll('rect')
    .data(data, (d) => d.name)

  group.value.selectAll('rect')
    .data(data, (d) => d.name)
    .enter()
    .append('rect')
    .attr('x', (d) => bandScale(d.name))
    .attr('y', height)
    .attr('width', bandScale.bandwidth())
    .attr('height', 0)
    .attr('fill', (d) => colorScale(d.age));
  
  group.value.selectAll('rect')
    .transition()
    .ease(easeBackInOut)
    .duration(3000)
    .attr('x', (d) => bandScale(d.name))
    .attr('y', (d) => ventasScale(d.ventas))
    .attr('width', bandScale.bandwidth())
    .attr('height', (d) => height - ventasScale(d.ventas))
    .attr('fill', (d) => colorScale(d.age));
}

onMounted(() => {
  constructChart();
  data[0].ventas = 13546384 + 120000000;
  data.push({ name: 'nuevo', ventas: 120000000, age: 18 })
  setTimeout(() => {
    updateChart();
  }, 2000);
});


</script>

<style lang="scss">
.chart {
  &__wrapper {
    width: 600px;
    height: 300px;
    background: white;
    color: black;
  }
  .axis {
    shape-rendering: crispEdges;
    line {
      stroke-width: 0.5px;
    }
    text {
      font-size: 8px;
    }
    .domain {
      display: none;
    }
  }
}
svg {
  background: #F0F0F0;
}
</style>
