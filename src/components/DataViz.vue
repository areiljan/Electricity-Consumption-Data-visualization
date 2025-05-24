<template>
  <svg ref="svg" width="800" height="400"></svg>
</template>

<script setup>
import { onMounted, ref } from 'vue'
import * as d3 from 'd3'

const svg = ref(null)

onMounted(async () => {
  // load your data
  const data = await d3.csv('/data/sample.csv', d3.autoType)

  const width  = 800
  const height = 400
  const margin = { top: 20, right: 20, bottom: 30, left: 40 }

  const x = d3.scaleTime()
    .domain(d3.extent(data, d => d.date))
    .range([margin.left, width - margin.right])

  const y = d3.scaleLinear()
    .domain([0, d3.max(data, d => d.value)]).nice()
    .range([height - margin.bottom, margin.top])

  const line = d3.line()
    .x(d => x(d.date))
    .y(d => y(d.value))

  const g = d3.select(svg.value)

  // axes
  g.append('g')
    .attr('transform', `translate(0,${height - margin.bottom})`)
    .call(d3.axisBottom(x))

  g.append('g')
    .attr('transform', `translate(${margin.left},0)`)
    .call(d3.axisLeft(y))

  // line path
  g.append('path')
    .datum(data)
    .attr('fill', 'none')
    .attr('stroke', 'steelblue')
    .attr('stroke-width', 1.5)
    .attr('d', line)
})
</script>

