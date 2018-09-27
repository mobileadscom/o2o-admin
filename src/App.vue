<template>
  <div id="app">
    <div class="header">
      <div class="title">BodyMainte Admin</div>
    </div>
    <div class="content">
      <router-view/>
    </div>
  </div>
</template>

<script>
export default {
  name: 'App'
}
</script>

<style>
html {
  font-family: "Source Sans Pro", -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, "Helvetica Neue", Arial, sans-serif;
  font-size: 16px;
  word-spacing: 1px;
  -ms-text-size-adjust: 100%;
  -webkit-text-size-adjust: 100%;
  -moz-osx-font-smoothing: grayscale;
  -webkit-font-smoothing: antialiased;
  box-sizing: border-box;
  color: #222;
}

*{
  box-sizing: border-box;
  margin: 0;
}

.header {
  width: 100%;
  background-color: #e82f22;
  height: 50px;
  -webkit-box-shadow: 0 3px 6px rgba(0,0,0,.16), 0 3px 6px rgba(0,0,0,.23);
  box-shadow: 0 3px 6px rgba(0,0,0,.16), 0 3px 6px rgba(0,0,0,.23);
  position: relative;
  z-index: 60;
  position: fixed;
  top: 0;
}

.header .title {
  color: white;
  font-size: 24px;
  margin-top: 10px;
  margin-left: 25px;
}

.content {
  margin: 60px 15px 20px;
}

.shadow-1 {
  -webkit-box-shadow: 0 1px 3px rgba(0,0,0,0.12), 0 1px 2px rgba(0,0,0,0.24);
  box-shadow: 0 1px 3px rgba(0,0,0,0.12), 0 1px 2px rgba(0,0,0,0.24);
}

.shadow-2 {
  -webkit-box-shadow: 0 3px 6px rgba(0,0,0,0.16), 0 3px 6px rgba(0,0,0,0.23);
  box-shadow: 0 3px 6px rgba(0,0,0,0.16), 0 3px 6px rgba(0,0,0,0.23);
}

.shadow-3 {
  -webkit-box-shadow: 0 10px 20px rgba(0,0,0,0.19), 0 6px 6px rgba(0,0,0,0.23);
  box-shadow: 0 10px 20px rgba(0,0,0,0.19), 0 6px 6px rgba(0,0,0,0.23);
}

.shaodw-4 {
  -webkit-box-shadow: 0 14px 28px rgba(0,0,0,0.25), 0 10px 10px rgba(0,0,0,0.22);
  box-shadow: 0 14px 28px rgba(0,0,0,0.25), 0 10px 10px rgba(0,0,0,0.22);
}

.shadow-5 {
  -webkit-box-shadow: 0 19px 38px rgba(0,0,0,0.30), 0 15px 12px rgba(0,0,0,0.22);
  box-shadow: 0 19px 38px rgba(0,0,0,0.30), 0 15px 12px rgba(0,0,0,0.22);
}

button.primary {
  margin: 10px;
  padding: 5px 10px;
  background-color: #4285F4;
  border: none;
  color: white;
  border-radius: 4px;
  box-sizing: border-box;
  transition: all 0.3s;
  font-size: 16px;
  outline: none;
  -webkit-box-shadow: 0 3px 6px rgba(0,0,0,0.16), 0 3px 6px rgba(0,0,0,0.23);
  box-shadow: 0 3px 6px rgba(0,0,0,0.16), 0 3px 6px rgba(0,0,0,0.23);
}

button.primary:hover {
  cursor: pointer;
  background-color: #00bd89;
}

button.primary:active, button.secondary:active {
  transform: translateY(3px);
  -webkit-box-shadow: 0 1px 3px rgba(0,0,0,0.12), 0 1px 2px rgba(0,0,0,0.24);
  box-shadow: 0 1px 3px rgba(0,0,0,0.12), 0 1px 2px rgba(0,0,0,0.24);
}

.secondary {
  display: inline-block;
  padding: 4px 12px 6px;
  background-color: #6a5acd;
  color: white;
  font-size: 14px;
  border-radius: 2px;
  -webkit-box-shadow: 0 1px 3px rgba(0,0,0,0.12), 0 1px 2px rgba(0,0,0,0.24);
  box-shadow: 0 1px 3px rgba(0,0,0,0.12), 0 1px 2px rgba(0,0,0,0.24);
}

.secondary:hover {
  cursor: pointer;
  background-color: #51449d;
}

@-webkit-keyframes spin {
  0% { -webkit-transform: rotate(0deg); }
  100% { -webkit-transform: rotate(360deg); }
}

@keyframes spin {
  0% { transform: rotate(0deg); }
  100% { transform: rotate(360deg); }
}

.form-loader {
  border-radius: 50%;
  margin: auto;
  -webkit-animation: spin 1s linear infinite;
  animation: spin 1s linear infinite;
  width: 50px;
  height: 50px;
  box-sizing: border-box;
  position: static;
  transform: none;
  border: 5px solid #94E2EF;
  border-top: 5px solid #1DA1F2;
}

.small-loader {
  border-radius: 50%;
  margin: auto;
  -webkit-animation: spin 1s linear infinite;
  animation: spin 1s linear infinite;
  width: 30px;
  height: 30px;
  -webkit-box-sizing: border-box;
  box-sizing: border-box;
  position: static;
  -webkit-transform: none;
  transform: none;
  border: 3px solid #94E2EF;
  border-top: 3px solid #1DA1F2;
}
</style>