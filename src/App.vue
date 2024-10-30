<template>
  <h1>MI COMPONENTE PRINCIPAL (App.vue)</h1>
  <h2>Menú de navegación</h2>
  <RouterLink to="/">INICIO</RouterLink> |
  
  <RouterLink to="/nosotros">NOSOTROS</RouterLink> |
  
  <RouterLink to="/servicios">SERVICIOS</RouterLink> |
  
  <RouterLink to="/auth/login">INGRESAR</RouterLink>

  <hr>
  <RouterView />

</template>