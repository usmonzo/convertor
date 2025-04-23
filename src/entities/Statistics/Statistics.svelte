<script lang="ts">
  import { getContext, onMount } from "svelte";
  import Chart from "chart.js/auto";
  import Loader from "../../shared/ui/Loader/Loader.svelte";

  let distance = "";
  let fuel = "";
  let comment = "";
  const theme = getContext<boolean>("isDarkMode");

  let loading = true;

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
    loading = true;
    const res = await fetch(`${import.meta.env.VITE_BASE_URL}/stats`);
    entries = await res.json();
    loading = false;
    if (!isMobile) updateChart();
  }

  async function submit() {
    loading = true;
    const entry = {
      distance: parseFloat(distance),
      fuel: parseFloat(fuel),
      comment,
      date: new Date().toISOString(),
    };

    await fetch(`${import.meta.env.VITE_BASE_URL}/stats`, {
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
    loading = true;
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
  <form on:submit|preventDefault={submit}>
    <input bind:value={distance} placeholder="Пробег (км)" required />
    <input bind:value={fuel} placeholder="Топливо (л)" required />
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
          <div class="{theme && 'dark '}entry-card-row}">
            <strong>Дата:</strong>
            <div class="dots"></div>
            <p>
              {new Date(entry.date).toLocaleDateString()}
            </p>
          </div>
          <div class="entry-card-row">
            <strong>Пробег:</strong>
            <div class="dots"></div>
            <p>
              {entry.distance} км
            </p>
          </div>
          <div class="entry-card-row">
            <strong>Топливо:</strong>
            <div class="dots"></div>
            <p>
              {entry.fuel} л
            </p>
          </div>
          {#if entry.comment}<div class="entry-card-row">
              <strong>Комментарий:</strong>
              <div class="dots"></div>
              <p>
                {entry.comment}
              </p>
            </div>{/if}
          <button on:click={() => deleteEntry(entry.id)}>Удалить</button>
        </div>
      {/each}
    </div>
  {:else if loading}
    <Loader />
  {:else}
    <div class="table-scroll">
      <table>
        <thead>
          <tr>
            <th>Дата</th>
            <th>Расстояние</th>
            <th>Потребление</th>
            <th>Тип топлива</th>
            <th></th>
          </tr>
        </thead>
        <tbody>
          {#each entries as entry}
            <tr>
              <td>{new Date(entry.date).toLocaleDateString()}</td>
              <td>{entry.distance} км</td>
              <td>{entry.fuel} л</td>
              <td>{entry.comment}</td>
              <td><button on:click={() => deleteEntry(entry.id)}>🗑</button></td
              >
            </tr>
          {/each}
        </tbody>
      </table>
    </div>
    }
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
    width: 100%;
    margin-top: 20px;
  }
  .chart-container {
    background-color: #fff;
    position: relative;
    width: 100%;
    height: 300px;
    margin-bottom: 24px;
  }
  .table-scroll {
    background: #fff;
    border-radius: 12px;
    padding: 16px;
    border: 1px solid #dce1e6;
    display: flex;
    flex-direction: column;
    align-items: center;
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
    border-bottom: 1px solid #030303;
    text-align: left;
    font-size: 15px;
    color: #030303;
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
    border: 1px solid #dce1e6;
    display: flex;
    flex-direction: column;
    align-items: center;
  }
  .entry-card-row {
    display: flex;
    justify-content: space-between;
    align-items: center;
    width: 100%;
    color: #030303;
    font-size: 1.1rem;
  }
  .dark {
    display: flex;
    justify-content: space-between;
    align-items: center;
    width: 100%;
    color: #fff;
    font-size: 1.1rem;
  }
  .dots {
    flex-grow: 1;
    border-bottom: 1px dotted #aaa;
    height: 100%;
    margin-bottom: 20px;
    align-self: flex-end;
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
