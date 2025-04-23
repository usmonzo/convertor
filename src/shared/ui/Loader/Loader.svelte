<script lang="ts">
  export let message: string = "Загрузка...";
</script>

<div class="loader-container">
  <div class="spinner"></div>
  <p>{message}</p>
</div>

<style>
  .loader-container {
    display: flex;
    flex-direction: column;
    align-items: center;
    justify-content: center;
    height: 100%;
    padding: 40px 0;
    text-align: center;
    color: #666;
  }

  .spinner {
    width: 48px;
    height: 48px;
    border: 4px solid #f0f0f0;
    border-top: 4px solid #ffdd2d;
    border-radius: 50%;
    animation: spin 1s linear infinite;
    margin-bottom: 16px;
  }

  @keyframes spin {
    0% {
      transform: rotate(0deg);
    }
    100% {
      transform: rotate(360deg);
    }
  }
</style>
