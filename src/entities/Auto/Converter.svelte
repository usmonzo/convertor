<script lang="ts">
  import "./Converter.css";

  let mpg: number | null = 0;
  let milesValue: number | null = 0;
  let gallonsValue: number | null = 0;

  let lper100km: number = 0;
  let kilometers: number = 0;
  let liters: number = 0;

  function convertMPGtoLPer100KM(mpg: number | null): number {
    if (!mpg) return 0;
    return 235.215 / mpg;
  }

  function convertMilesToKm(miles: number | null): number {
    if (!miles) return 0;
    return miles * 1.60934;
  }

  function convertGallonsToLiters(gallons: number | null): number {
    if (!gallons) return 0;
    return gallons * 3.78541;
  }
  function clearIfZero(val: number | null) {
    if (val === 0) val = null;
  }

  function restoreIfEmpty(val: number | null) {
    if (val === null || val === undefined || isNaN(val)) val = 0;
  }

  $: lper100km = convertMPGtoLPer100KM(mpg);
  $: kilometers = convertMilesToKm(milesValue);
  $: liters = convertGallonsToLiters(gallonsValue);
</script>

<div class="converter-container">
  <div class="conversion-group">
    <div class="conversion-card">
      <div class="card-content">
        <div class="input-wrapper">
          <label class="input-label" for="mpg">MPG</label>
          <input
            id="mpg"
            bind:value={mpg}
            class="tinkoff-input"
            on:focus={() => {
              if (mpg === 0) mpg = null;
            }}
            on:blur={() => {
              if (mpg === null || mpg === undefined || isNaN(mpg)) mpg = 0;
            }}
          />
        </div>
        <!-- <div class="arrow">→</div> -->
        <div class="result-wrapper">
          <span class="result-value">{lper100km.toFixed(2)}</span>
          <span class="result-label">L/100km</span>
        </div>
      </div>
    </div>

    <div class="conversion-card">
      <div class="card-content">
        <div class="input-wrapper">
          <label class="input-label" for="miles">Мили</label>
          <input
            id="miles"
            bind:value={milesValue}
            class="tinkoff-input"
            on:focus={() => {
              if (milesValue === 0) milesValue = null;
            }}
            on:blur={() => {
              if (
                milesValue === null ||
                milesValue === undefined ||
                isNaN(milesValue)
              )
                milesValue = 0;
            }}
          />
        </div>
        <!-- <div class="arrow">→</div> -->
        <div class="result-wrapper">
          <span class="result-value">{kilometers.toFixed(2)}</span>
          <span class="result-label">Километры</span>
        </div>
      </div>
    </div>

    <div class="conversion-card">
      <div class="card-content">
        <div class="input-wrapper">
          <label class="input-label" for="gallons">Галлоны</label>
          <input
            id="gallons"
            bind:value={gallonsValue}
            class="tinkoff-input"
            on:focus={() => {
              if (gallonsValue === 0) gallonsValue = null;
            }}
            on:blur={() => {
              if (
                gallonsValue === null ||
                gallonsValue === undefined ||
                isNaN(gallonsValue)
              )
                gallonsValue = 0;
            }}
          />
        </div>
        <!-- <div class="arrow">→</div> -->
        <div class="result-wrapper">
          <span class="result-value">{liters.toFixed(2)}</span>
          <span class="result-label">Литры</span>
        </div>
      </div>
    </div>
  </div>
</div>

<style>
</style>
