<script lang="ts">
  import { onMount } from "svelte";
  import Chart from "chart.js/auto";

  let distance = "";
  let fuel = "";
  let comment = "";

  let entries: Array<{
    id: string;
    distance: number;
    fuel: number;
    comment?: string;
    date: string;
  }> = [];

  let chart;
  let isMobile = false;

  onMount(() => {
    isMobile = window.innerWidth < 600;
    loadStats();
  });

  async function loadStats() {
    const res = await fetch(`${import.meta.env.VITE_BASE_URL}}/stats`);
    entries = await res.json();
    if (!isMobile) updateChart();
  }

  async function submit() {
    const entry = {
      distance: parseFloat(distance),
      fuel: parseFloat(fuel),
      comment,
      date: new Date().toISOString(),
    };

    await fetch(`${import.meta.env.VITE_BASE_URL}}/stats`, {
      method: "POST",
      headers: { "Content-Type": "application/json" },
      body: JSON.stringify(entry),
    });

    distance = "";
    fuel = "";
    comment = "";
    await loadStats();
  }

  async function deleteEntry(id: string) {
    await fetch(`${import.meta.env.VITE_BASE_URL + "/stats/" + id}`, {
      method: "DELETE",
    });
    await loadStats();
  }

  function updateChart() {
    const ctx = document.getElementById("chart") as HTMLCanvasElement;
    if (chart) chart.destroy();
    chart = new Chart(ctx, {
      type: "line",
      data: {
        labels: entries.map((e) => new Date(e.date).toLocaleDateString()),
        datasets: [
          {
            label: "Расход (л/100км)",
            data: entries.map((e) => ((e.fuel / e.distance) * 100).toFixed(2)),
            borderColor: "#FFDD2D",
            backgroundColor: "rgba(255,221,45,0.2)",
            tension: 0.4,
          },
        ],
      },
      options: {
        responsive: true,
        maintainAspectRatio: false,
      },
    });
  }
</script>

<div class="converter-wrapper">
  <h2>Статистика расхода топлива</h2>

  <form on:submit|preventDefault={submit}>
    <input
      type="number"
      bind:value={distance}
      placeholder="Пробег (км)"
      required
    />
    <input type="number" bind:value={fuel} placeholder="Топливо (л)" required />
    <input
      type="text"
      bind:value={comment}
      placeholder="Комментарий (необязательно)"
    />
    <button type="submit">Добавить</button>
  </form>

  {#if !isMobile}
    <div class="chart-container">
      <canvas id="chart"></canvas>
    </div>
  {/if}

  {#if isMobile}
    <div class="card-list">
      {#each entries as entry}
        <div class="entry-card">
          <div>
            <strong>Дата:</strong>
            {new Date(entry.date).toLocaleDateString()}
          </div>
          <div><strong>Пробег:</strong> {entry.distance} км</div>
          <div><strong>Топливо:</strong> {entry.fuel} л</div>
          {#if entry.comment}<div>
              <strong>Комментарий:</strong>
              {entry.comment}
            </div>{/if}
          <button on:click={() => deleteEntry(entry.id)}>Удалить</button>
        </div>
      {/each}
    </div>
  {:else}
    <div class="table-scroll">
      <table>
        <thead>
          <tr>
            <th>Дата</th>
            <th>Км</th>
            <th>Литры</th>
            <th>Комментарий</th>
            <th></th>
          </tr>
        </thead>
        <tbody>
          {#each entries as entry}
            <tr>
              <td>{new Date(entry.date).toLocaleDateString()}</td>
              <td>{entry.distance}</td>
              <td>{entry.fuel}</td>
              <td>{entry.comment}</td>
              <td><button on:click={() => deleteEntry(entry.id)}>🗑</button></td
              >
            </tr>
          {/each}
        </tbody>
      </table>
    </div>
  {/if}
</div>

<style>
  .converter-wrapper {
    padding: 16px;
  }
  form {
    display: flex;
    gap: 8px;
    margin-bottom: 24px;
    flex-wrap: wrap;
  }
  input {
    padding: 16px;
    border: 2px solid #f0f0f0;
    border-radius: 12px;
    font-size: 18px;
    color: #333;
    transition: all 0.2s ease;
    background: #fff;
    width: 100%;
  }
  button {
    background: #ffdd2d;
    padding: 12px 16px;
    border: none;
    border-radius: 8px;
    cursor: pointer;
    font-size: 16px;
    color: #030303;
  }
  .chart-container {
    position: relative;
    width: 100%;
    height: 300px;
    margin-bottom: 24px;
  }
  .table-scroll {
    overflow-x: auto;
  }
  table {
    width: 100%;
    border-collapse: collapse;
    margin-top: 24px;
    min-width: 600px;
  }
  th,
  td {
    padding: 10px 14px;
    border-bottom: 1px solid #eee;
    text-align: left;
    font-size: 15px;
  }
  .card-list {
    display: flex;
    flex-direction: column;
    gap: 12px;
    margin-top: 16px;
  }
  .entry-card {
    background: #fff;
    border-radius: 12px;
    padding: 16px;
    box-shadow: 0 2px 6px rgba(0, 0, 0, 0.1);
    font-size: 15px;
    color: #030303;
  }
  .entry-card button {
    margin-top: 8px;
    background: #ff4d4f;
    color: white;
    border: none;
    padding: 8px 12px;
    border-radius: 6px;
    font-size: 14px;
  }
</style>
