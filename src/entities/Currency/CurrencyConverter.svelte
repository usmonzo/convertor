<script lang="ts">
  import { onMount } from "svelte";
  import "./CurrencyConverter.css";

  let amount: number | null = 0;
  let fromCurrency: string = "RUB";
  let loading: boolean = true;
  let error: string = "";
  let lastUpdate: string = "";

  type CurrencyData = {
    [key: string]: string;
  };

  interface ExchangeRates {
    [key: string]: {
      [key: string]: number;
    };
  }

  let rates: ExchangeRates = {};

  const currencySymbols: CurrencyData = {
    RUB: "₽",
    USD: "$",
    EUR: "€",
    TJS: "TJS",
  };

  const currencyNames: CurrencyData = {
    RUB: "Российский рубль",
    USD: "Доллар США",
    EUR: "Евро",
    TJS: "Таджикский сомони",
  };

  const currencyColors: CurrencyData = {
    RUB: "#2196F3",
    USD: "#4CAF50",
    EUR: "#FFC107",
    TJS: "#9C27B0",
  };

  async function fetchExchangeRates() {
    try {
      loading = true;
      error = "";

      const response = await fetch(
        "https://api.exchangerate-api.com/v4/latest/USD",
      );
      const data = await response.json();
      const baseRates = data.rates;
      rates = {
        USD: {
          RUB: baseRates.RUB,
          EUR: baseRates.EUR,
          TJS: baseRates.TJS,
        },
        RUB: {
          USD: 1 / baseRates.RUB,
          EUR: baseRates.EUR / baseRates.RUB,
          TJS: baseRates.TJS / baseRates.RUB,
        },
        EUR: {
          USD: 1 / baseRates.EUR,
          RUB: baseRates.RUB / baseRates.EUR,
          TJS: baseRates.TJS / baseRates.EUR,
        },
        TJS: {
          USD: 1 / baseRates.TJS,
          RUB: baseRates.RUB / baseRates.TJS,
          EUR: baseRates.EUR / baseRates.TJS,
        },
      };

      lastUpdate = new Date().toLocaleString("ru-RU");
    } catch (e) {
      error = "Не удалось загрузить курсы валют";
      rates = {
        RUB: { USD: 0.011, EUR: 0.0097, TJS: 0.12 },
        USD: { RUB: 91.23, EUR: 0.89, TJS: 10.98 },
        EUR: { RUB: 102.45, USD: 1.12, TJS: 12.34 },
        TJS: { RUB: 8.32, USD: 0.091, EUR: 0.081 },
      };
    } finally {
      loading = false;
    }
  }

  function convert(amount: number | null, from: string, to: string): number {
    if (from === to) return amount!;
    return amount! * rates[from][to];
  }

  let convertedAmounts: { currency: string; amount: number }[] = [];

  $: {
    if (amount && fromCurrency && rates[fromCurrency]) {
      const currencies = Object.keys(currencySymbols);
      convertedAmounts = currencies
        .filter((currency) => currency !== fromCurrency)
        .map((currency) => ({
          currency,
          amount: convert(amount, fromCurrency, currency),
        }));
    } else {
      convertedAmounts = [];
    }
  }

  function formatAmount(amount: number): string {
    return new Intl.NumberFormat("ru-RU", {
      maximumFractionDigits: 2,
      minimumFractionDigits: 2,
    }).format(amount);
  }

  onMount(() => {
    fetchExchangeRates();
    // Обновляем курсы каждый час
    const interval = setInterval(fetchExchangeRates, 3600000);
    return () => clearInterval(interval);
  });
</script>

<div class="currency-converter">
  <div class="input-section">
    <div class="amount-input">
      <span class="input-label">Сумма для конвертации</span>
      <div class="input-wrapper">
        <input
          bind:value={amount}
          placeholder="0"
          min="0"
          class="tinkoff-input"
          on:focus={() => {
            if (amount === 0) amount = null;
          }}
          on:blur={() => {
            if (amount === null || amount === undefined || isNaN(amount))
              amount = 0;
          }}
        />
        <select bind:value={fromCurrency} class="currency-select">
          {#each Object.keys(currencySymbols) as currency}
            <option value={currency}>
              {currency} ({currencySymbols[currency]})
            </option>
          {/each}
        </select>
      </div>
    </div>
  </div>

  {#if loading}
    <div class="loading-state">
      <div class="loader"></div>
      <p>Загрузка курсов валют...</p>
    </div>
  {:else if error}
    <div class="error-state">
      <p>{error}</p>
    </div>
  {:else}
    <div class="results-section">
      {#if amount! > 0}
        {#each convertedAmounts as conversion}
          <div
            class="result-card"
            style="border-left: 4px solid {currencyColors[conversion.currency]}"
          >
            <div class="result-amount">
              <span class="converted-amount"
                >{formatAmount(conversion.amount)}</span
              >
              <span class="currency-symbol"
                >{currencySymbols[conversion.currency]}</span
              >
            </div>
            <div class="currency-name">
              {currencyNames[conversion.currency]}
            </div>
            <div class="rate-info">
              1 {fromCurrency} = {formatAmount(
                rates[fromCurrency][conversion.currency],
              )}
              {conversion.currency}
            </div>
          </div>
        {/each}
      {:else}
        <div class="empty-state">
          <span class="empty-icon">💱</span>
          <p>Введите сумму для конвертации</p>
        </div>
      {/if}
    </div>
  {/if}

  {#if lastUpdate}
    <div class="update-info">
      Последнее обновление курсов: {lastUpdate}
    </div>
  {/if}
</div>
