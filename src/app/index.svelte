<script lang="ts">
  import { setContext } from "svelte";
  import logo from "../shared/assets/logo.svg";
  import Footer from "../shared/ui/Footer/Footer.svelte";
  import Converter from "../entities/Auto/Converter.svelte";
  import CurrencyConverter from "../entities/Currency/CurrencyConverter.svelte";

  let activeTab: "auto" | "currency" | "measures" = "auto";
  let isDarkMode = false;
  setContext("isDarkMode", isDarkMode);

  function switchTab(tab: "auto" | "currency" | "measures") {
    activeTab = tab;
  }

  function toggleTheme() {
    isDarkMode = !isDarkMode;
    document.body.classList.toggle("dark-mode");
  }
</script>

<main class:dark-mode={isDarkMode}>
  <div class="page-header">
    <div class="header-content">
      <div class="header-left">
        <img src={logo} alt="Convertor Logo" class="logo" />
      </div>
      <div class="tabs">
        <button
          class="tab-button {activeTab === 'auto' ? 'active' : ''}"
          on:click={() => switchTab("auto")}
        >
          <span class="tab-icon">🚘</span>
          Авто
        </button>
        <button
          class="tab-button {activeTab === 'currency' ? 'active' : ''}"
          on:click={() => switchTab("currency")}
        >
          <span class="tab-icon">💱</span>
          Валюты
        </button>
        <button
          class="tab-button {activeTab === 'measures' ? 'active' : ''}"
          on:click={() => switchTab("measures")}
        >
          <span class="tab-icon">📊</span>
          Статистика
        </button>
      </div>
      <div class="header-right">
        <button class="theme-toggle" on:click={toggleTheme}>
          {isDarkMode ? "🌞" : "🌙"}
        </button>
      </div>
    </div>
  </div>

  <div class="content-container">
    <div class="tab-content-outer">
      {#key activeTab}
        <div class="tab-panel">
          >
          {#if activeTab === "auto"}
            <div class="section-header">
              <h2>Автомобильные величины</h2>
              <p class="section-description">
                Конвертируйте единицы измерения для вашего автомобиля
              </p>
            </div>

            <div class="converter-wrapper">
              <Converter />
            </div>

            <div class="info-card">
              <div class="info-icon">ℹ️</div>
              <p class="info-text">
                Все расчёты производятся автоматически при вводе значений.
                Результаты округляются до двух знаков после запятой.
              </p>
            </div>
          {:else if activeTab === "currency"}
            <div class="section-header">
              <h2>Конвертер валют</h2>
              <p class="section-description">
                Актуальные курсы основных валют с ежечасным обновлением
              </p>
            </div>

            <div class="converter-wrapper">
              <CurrencyConverter />
            </div>
          {:else}
            <div class="section-header">
              <h2>Меры измерения</h2>
              <p class="section-description">
                Этот раздел находится в разработке
              </p>
            </div>
          {/if}
        </div>
      {/key}
    </div>
  </div>
  <Footer />
</main>

<style>
  :global(body) {
    background-color: #f8f9fa;
    margin: 0;
    font-family:
      system-ui,
      -apple-system,
      BlinkMacSystemFont,
      "Segoe UI",
      Roboto,
      Oxygen,
      Ubuntu,
      Cantarell,
      sans-serif;
    transition: background-color 0.3s ease;
  }

  :global(body.dark-mode) {
    background-color: #1a1a1a;
    color: #fff;
  }

  main {
    min-height: 100dvh;
    display: flex;
    flex-direction: column;
    width: 100%;
  }

  .page-header {
    position: sticky;
    top: 16px;
    margin-top: 16px;
    background: #ffffff;
    box-shadow: 0 2px 8px rgba(0, 0, 0, 0.05);
    padding: 16px 0;
    border-radius: 18px;
    z-index: 100;
    width: 100%;
    max-width: 1000px;
    align-self: center;
  }

  :global(.dark-mode) .page-header {
    background: #2d2d2d;
    box-shadow: 0 2px 8px rgba(0, 0, 0, 0.2);
  }

  .header-content {
    max-width: 1200px;
    margin: 0 auto;
    padding: 0 24px;
    display: flex;
    align-items: center;
    justify-content: space-between;
  }

  .header-left,
  .header-right {
    flex: 1;
  }

  .header-right {
    display: flex;
    justify-content: flex-end;
  }

  .theme-toggle {
    background: none;
    border: none;
    font-size: 24px;
    cursor: pointer;
    padding: 8px;
    border-radius: 50%;
    transition: background-color 0.3s ease;
  }

  .theme-toggle:hover {
    background-color: rgba(0, 0, 0, 0.05);
  }

  :global(.dark-mode) .theme-toggle:hover {
    background-color: rgba(255, 255, 255, 0.1);
  }

  .tabs {
    display: flex;
    gap: 8px;
  }

  .tab-button {
    padding: 10px 20px;
    border: none;
    background: none;
    border-radius: 12px;
    color: #666;
    font-size: 15px;
    cursor: pointer;
    transition: all 0.2s ease;
    display: flex;
    align-items: center;
    gap: 8px;
  }

  .tab-icon {
    font-size: 18px;
  }

  .tab-button:hover {
    background: rgba(255, 221, 45, 0.1);
    color: #333;
  }

  .tab-button.active {
    background: #ffdd2d;
    color: #333;
    font-weight: 500;
  }

  :global(.dark-mode) .tab-button {
    color: #999;
  }

  :global(.dark-mode) .tab-button:hover {
    background: rgba(255, 221, 45, 0.2);
    color: #fff;
  }

  :global(.dark-mode) .tab-button.active {
    background: #ffdd2d;
    color: #333;
  }

  .content-container {
    max-width: 1200px;
    margin: 0 auto;
    padding: 40px 24px;
    flex-grow: 1;
    display: flex;
    flex-direction: column;
    overflow: hidden;
  }
  .tab-content-outer {
    position: relative;
    flex-grow: 1;
    min-height: 600px;
    display: flex;
    flex-direction: column;
    overflow: hidden;
    transform: translateZ(0);
  }

  .tab-panel {
    width: 100%;
    flex: 1;
    display: flex;
    flex-direction: column;
    will-change: opacity;
    backface-visibility: hidden;
    transform: translateZ(0);
  }

  .section-header {
    margin-bottom: 40px;
    text-align: center;
  }

  h2 {
    font-size: 32px;
    font-weight: 600;
    color: #333;
    margin: 0 0 12px 0;
  }

  :global(.dark-mode) h2 {
    color: #fff;
  }

  .section-description {
    color: #666;
    margin: 0;
    font-size: 16px;
    line-height: 1.5;
  }

  :global(.dark-mode) .section-description {
    color: #999;
  }

  .converter-wrapper {
    margin-bottom: 32px;
  }

  .info-card {
    background: #fff;
    border-radius: 16px;
    padding: 20px;
    display: flex;
    align-items: flex-start;
    gap: 16px;
    margin-top: 32px;
    box-shadow: 0 4px 12px rgba(0, 0, 0, 0.05);
  }

  :global(.dark-mode) .info-card {
    background: #2d2d2d;
    box-shadow: 0 4px 12px rgba(0, 0, 0, 0.2);
  }

  .info-icon {
    font-size: 24px;
  }

  .info-text {
    margin: 0;
    font-size: 15px;
    color: #666;
    line-height: 1.6;
  }

  :global(.dark-mode) .info-text {
    color: #999;
  }

  @media (max-width: 1200px) {
    .tab-content-outer {
      min-height: 650px;
    }
  }

  @media (max-width: 960px) {
    .tab-content-outer {
      min-height: 700px;
    }

    .section-header {
      margin-bottom: 32px;
    }
  }

  @media (max-width: 768px) {
    .header-content {
      flex-direction: column;
      gap: 16px;
    }

    .header-left {
      text-align: center;
    }

    .tabs {
      width: 100%;
      justify-content: center;
      overflow-x: auto;
      padding-bottom: 8px;
    }

    .header-right {
      position: absolute;
      top: 16px;
      right: 16px;
    }

    .tab-content-outer {
      min-height: 800px;
    }

    h2 {
      font-size: 28px;
    }

    .section-header {
      margin-bottom: 24px;
    }
  }

  @media (max-width: 480px) {
    .tab-content-outer {
      min-height: 900px;
    }

    .content-container {
      padding: 32px 16px;
    }

    h2 {
      font-size: 26px;
    }

    .section-header {
      margin-bottom: 20px;
    }

    .tab-button {
      padding: 8px 16px;
      font-size: 14px;
    }
  }
</style>
