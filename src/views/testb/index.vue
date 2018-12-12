<template>
  <div class="testB">

    <boxblue-component></boxblue-component>
    <boxyellow-component></boxyellow-component>

  </div>
</template>

<script>
import BoxblueComponent from './boxblue';
import BoxyellowComponent from './boxyellow';
export default {
    components: {
         BoxblueComponent,
         BoxyellowComponent,
    }
};

</script>

<style lang="scss" scoped>
.testB{
    margin:20px;

    .row{
        padding: 10px;
        text-align: center;
    }
}
</style>
