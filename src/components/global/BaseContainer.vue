<template lang="pug">
.flex.flex-col.min-h-screen
  slot(name="header")
  slot(name="main")
  slot(name="footer")
</template>