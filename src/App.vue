<template>
  <div>
    <div id="slogan" class="text-center">
      <h1>Gerador de nomes</h1>
      <h6>Gerador de nomes utilizando Vue, Node e GraphQL</h6>
    </div>
    <div id="main">
      <DominioList></DominioList>
    </div>
  </div>
</template>

<script>
import "bootstrap/dist/css/bootstrap.css";
import "font-awesome/css/font-awesome.css";
import DominioList from "./components/DominioList";

export default {
	name: "app",
	components:{
		DominioList
	}
};
</script>

<style>
  #slogan{
    margin: 30px 0px;
  }
  #main{
    background-color: #f1f1f1;
    padding: 30px 0px;
  }
</style>
