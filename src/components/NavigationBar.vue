<template>
<nav class="navbar navbar-expand-lg navbar-dark bg-dark">
  <a class="navbar-brand" href="main">TechpitNotion</a>
  <button class="navbar-toggler" type="button" data-toggle="collapse" data-target="#navbarText" aria-controls="navbarText" aria-expanded="false" aria-label="Toggle navigation">

    <span class="navbar-toggler-icon"></span>
  </button>

  <div class="collapse navbar-collapse" id="navbarText">
    <ul class="navbar-nav me-auto">
      <li class="nav-item">
        <a class="nav-link" href="/main"><i class="fas fa-yin-yang"></i>ノートリスト</a>
      </li>
      <li class="nav-item dropdown">
        <a class="nav-link dropdown-toggle" href="#" role="button" data-toggle="dropdown" aria-expanded="false">
          <i class="fas fa-pencil-alt"></i>学習用ページ
        </a>
        <ul class="dropdown-menu">
          <li><a class="dropdown-item" href="/study/binding">学習１</a></li>
          <li><a class="dropdown-item" href="/study/directive">学習２</a></li>
          <li><a class="dropdown-item" href="/study/event">学習３</a></li>
        </ul>
      </li>
    </ul>
  </div>
</nav>
</template>
  
  <script>
  export default {
  }
  </script>
  
  <style scoped>
  .navigation-bar {
    height: 60px;
    /*
    color: white;
    background-color: #6c757d;
    */
  }
  </style>
  