<div class="navbar navbar-fixed-top">
  <div class="navbar-inner">
    <div class="container">
        <ul class="nav">
          <li{% if page.nav == 'index' %} class="active"{% endif %}>
            <a href="/">Главная</a>
          </li>
          <li{% if page.nav == 'about' %} class="active"{% endif %}>
            <a href="/about.html">Обо мне</a>
          </li>
        </ul>
    </div>
  </div>
</div>
