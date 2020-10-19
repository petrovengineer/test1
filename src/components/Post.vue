<template>
     <article class="notification is-success">
      <div class="content">
        <p class="title">{{post.title}}</p>
        <div class="content">
            {{post.body}}
        </div>
      </div>
    </article>
</template>

<script>
export default {
  name: 'HelloWorld',
  props: {
    post: Object
  }
}
</script>

<style scoped>

</style>
