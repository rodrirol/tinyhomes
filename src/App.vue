<template>
  <div id="app">
    <nav class="navbar navbar-expand-lg navbar-light bg-light">
      <div class="container">
        <router-link class="navbar-brand" to="/">Tiny Home Builder</router-link>
        <button class="navbar-toggler" type="button" data-bs-toggle="collapse" data-bs-target="#navbarNav">
          <span class="navbar-toggler-icon"></span>
        </button>
        <div class="collapse navbar-collapse" id="navbarNav">
          <ul class="navbar-nav">
            <li class="nav-item">
              <router-link class="nav-link" to="/">Home</router-link>
            </li>
            <li class="nav-item">
              <router-link class="nav-link" to="/about">About Us</router-link>
            </li>
            <li class="nav-item">
              <router-link class="nav-link" to="/mvp">The MVP</router-link>
            </li>
          </ul>
        </div>
      </div>
    </nav>

    <router-view></router-view>

    <footer class="footer mt-auto py-3 bg-light">
      <div class="container text-center">
        <span class="text-muted">&copy; {{ new Date().getFullYear() }} Tiny Home Builder. All rights reserved.</span>
        <ul class="list-inline">
          <li class="list-inline-item"><a href="/about">About Us</a></li>
          <li class="list-inline-item"><a href="#">Contact</a></li>
          <li class="list-inline-item"><a href="#">Privacy Policy</a></li>
        </ul>
      </div>
    </footer>
  </div>
</template>

<style scoped>
#app {
  display: flex;
  flex-direction: column;
  min-height: 100vh; /* Ensure full viewport height */
}

#content-wrapper {
  flex: 1; /* Allow content to take up available space */
}

.footer {
  /* No need for fixed positioning anymore */
  width: 100%;
}
</style>

