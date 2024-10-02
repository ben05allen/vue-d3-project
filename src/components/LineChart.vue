<script setup lang="ts">
import * as d3 from "d3";
import { ref, onMounted } from "vue";

interface DataPoint {
  date: string;
  amount: number;
}

const chart = ref(null);

const chartData: DataPoint[] = [
  { date: "2007-04-24", amount: 93.24 },
  { date: "2007-04-25", amount: 95.35 },
  { date: "2007-04-26", amount: 98.84 },
  { date: "2007-04-27", amount: 99.92 },
  { date: "2007-04-30", amount: 99.8 },
  { date: "2007-05-01", amount: 99.47 },
  { date: "2007-05-02", amount: 100.39 },
  { date: "2007-05-03", amount: 100.4 },
  { date: "2007-05-04", amount: 100.81 },
  { date: "2007-05-07", amount: 103.92 },
  { date: "2007-05-08", amount: 105.06 },
  { date: "2007-05-09", amount: 106.88 },
  { date: "2007-05-10", amount: 107.34 },
];

const width = 800;
const height = 500;
const marginTop = 20;
const marginRight = 30;
const marginBottom = 40;
const marginLeft = 40;

const parseDate = d3.timeParse("%Y-%m-%d");

onMounted(() => {
  const parsedData = chartData.map((d) => ({
    ...d,
    date: parseDate(d.date) as Date,
  }));

  const svg = d3
    .select(chart.value)
    .append("svg")
    .attr("width", width)
    .attr("height", height);

  const g = svg.append("g");

  const x = d3
    .scaleTime()
    .domain(d3.extent(parsedData, (d) => d.date) as [Date, Date])
    .range([marginLeft, width - marginRight]);

  const y = d3
    .scaleLinear()
    .domain([
      Math.trunc((d3.min(parsedData, (d) => d.amount) as number) / 5) * 5,
      Math.trunc((d3.max(parsedData, (d) => d.amount) as number) / 5 + 1) * 5,
    ])
    .range([height - marginBottom, marginTop]);

  const line = d3
    .line()
    .x((d) => x(d.date))
    .y((d) => y(d.amount));

  g.append("g")
    .attr("transform", `translate(0, ${height - marginBottom})`)
    .call(d3.axisBottom(x).ticks(d3.timeDay.every(1)))
    .selectAll("text") // Rotate text for better readability if needed
    .attr("transform", "rotate(-45)")
    .style("text-anchor", "end");

  g.append("g")
    .attr("transform", `translate(${marginLeft},0)`)
    .call(d3.axisLeft(y).ticks(5))
    .append("text")
    .attr("fill", "#000")
    .attr("transform", "rotate(-90)")
    .attr("y", 6)
    .attr("dy", "1em")
    .attr("text-anchor", "end")
    .text("Price ($)");

  g.append("path")
    .datum(parsedData)
    .attr("fill", "none")
    .attr("stroke", "steelblue")
    .attr("stroke-width", 1.5)
    .attr("d", line);
});
</script>

<template>
  <div>
    <h1>My VueJS and D3 Project</h1>
    <div ref="chart"></div>
  </div>
</template>

<style scoped></style>
