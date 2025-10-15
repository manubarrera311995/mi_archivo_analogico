<script>
import Papa from 'papaparse';
import { onMount } from 'svelte';

let items = [];

function parseFecha(fechaStr) {
  if (!fechaStr) return null;
  const [dia, mes, año] = fechaStr.split('/');
  return new Date(`${año}-${mes}-${dia}`);
}

onMount(async () => {
  const res = await fetch('/data/metadata.csv');
  const text = await res.text();
  const parsed = Papa.parse(text, { header: true });
  items = parsed.data
    .filter(item => item.fecha_de_adquisicion)
    .map(item => ({
      ...item,
      fechaObj: parseFecha(item.fecha_de_adquisicion)
    }))
    .sort((a, b) => a.fechaObj - b.fechaObj);
});
</script>

<style>
.timeline {
  display: flex;
  flex-direction: column;
  gap: 1rem;
  margin: 2rem 0;
}
.timeline-item {
  padding: 1rem;
  border-left: 3px solid #888;
  margin-left: 1rem;
  position: relative;
}
.timeline-item::before {
  content: '';
  position: absolute;
  left: -1.2rem;
  top: 1rem;
  width: 0.8rem;
  height: 0.8rem;
  background: #888;
  border-radius: 50%;
}
.date {
  font-weight: bold;
  color: #555;
}
.label {
  font-size: 1.1rem;
  color: #222;
}
</style>

<div class="timeline">
  {#each items as item}
    <div class="timeline-item">
      <div class="date">{item.fecha_de_adquisicion}</div>
      <div class="label">{item.label}</div>
      <div>{item.articulo}</div>
    </div>
  {/each}
</div>
