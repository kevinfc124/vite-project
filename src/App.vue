<template>
  <header>
    <div class="wrapper">
      <h1 class="logo">MovieTrade</h1>
      <nav>
        <RouterLink to="/" active-class="router-link-exact-active">Home</RouterLink>
        <RouterLink to="/favorites" active-class="router-link-exact-active">Favorites</RouterLink>
      </nav>
    </div>
  </header>

  <RouterView />
</template>