<script lang="ts">
  import { getContext } from "svelte";
  import "./Footer.css";

  const isDarkMode = getContext("isDarkMode");
</script>

<footer class:dark-mode={isDarkMode}>
  <div class="footer-content">
    <div class="footer-left">
      <span class="footer-logo">
        <svg
          width="140"
          height="32"
          viewBox="0 0 140 32"
          fill="none"
          xmlns="http://www.w3.org/2000/svg"
        >
          <path d="M12 0H20L28 32H20L12 0Z" fill="#FFDD2D" />
          <path d="M0 8H8L16 24H8L0 8Z" fill="#FFDD2D" fill-opacity="0.6" />
          <path d="M24 8H32L40 24H32L24 8Z" fill="#FFDD2D" fill-opacity="0.6" />
          <text
            x="50"
            y="22"
            font-family="system-ui"
            font-size="18"
            font-weight="600"
            fill={isDarkMode ? "#FFFFFF" : "#333333"}>Convertor</text
          >
        </svg>
      </span>
      <p>© 2024 Convertor</p>
    </div>
    <div class="footer-links">
      <a href="#help">Помощь</a>
      <a href="#about">О сервисе</a>
      <a href="#contact">Контакты</a>
    </div>
  </div>
</footer>

<style>
  footer {
    background: #fff;
    padding: 32px 0;
    margin-top: auto;
    transition: background-color 0.3s ease;
  }

  :global(.dark-mode) footer {
    background: #2d2d2d;
  }

  .footer-content {
    max-width: 1200px;
    margin: 0 auto;
    padding: 0 24px;
    display: flex;
    justify-content: space-between;
    align-items: center;
  }

  .footer-left {
    display: flex;
    align-items: center;
    gap: 16px;
  }

  .footer-left p {
    margin: 0;
    color: #666;
    font-size: 14px;
  }

  :global(.dark-mode) .footer-left p {
    color: #999;
  }

  .footer-links {
    display: flex;
    gap: 24px;
  }

  .footer-links a {
    color: #666;
    text-decoration: none;
    font-size: 14px;
    transition: color 0.2s ease;
  }

  .footer-links a:hover {
    color: #ffdd2d;
  }

  :global(.dark-mode) .footer-links a {
    color: #999;
  }

  :global(.dark-mode) .footer-links a:hover {
    color: #ffdd2d;
  }

  @media (max-width: 768px) {
    .footer-content {
      flex-direction: column;
      gap: 24px;
      text-align: center;
    }

    .footer-left {
      flex-direction: column;
    }

    .footer-links {
      justify-content: center;
    }
  }
</style>
