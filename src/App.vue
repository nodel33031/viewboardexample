<template>
  <!-- <div id="nav">
    <router-link to="/">Home</router-link> |
    <router-link to="/about">About</router-link>
  </div> -->
  <router-view />
</template>

<style lang="less">
* {
  box-sizing: border-box;
  padding: 0;
  margin: 0;
}
#app {
  font-family: "Avenir", Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  color: #2c3e50;
}

.navbar {
  text-align: center;
  line-height: 60px;
  height: 60px;
  position: fixed;
  left: 50%;
  top: 0;
  transform: translateX(-50%);
  width: 100%;
  background-color: white;
  transition: all 0.1s;
  li {
    font-size: 20px;
    margin: 0 40px;
    padding: 0 20px;
    display: inline-block;
    list-style: none;
    &:hover {
      background-color: #efefef;
      cursor: pointer;
    }
  }
}

.toolbar {
  position: fixed;
  bottom: 100px;
  left: 50%;
  transform: translateX(-50%);
  width: 70%;
  background-color: #fff;
  height: 100px;
  border-radius: 50px;
  li {
    line-height: 100px;
    margin: 0;
    padding: 0 20px;
    list-style: none;
    display: inline-block;
    i {
      font-size: 20px;
      padding: 30px;
      border-radius: 50px;
      &:hover {
        background-color: #d8d8d8;
      }
    }
  }
}
.toolbar__size {
  font-size: 20px;
  span {
    margin: 0 20px;
  }
  input {
    text-align: center;
    background-color: #d8d8d8;
    padding: 10px 20px;
    width: 100px;
    font-size: 20px;
    border-radius: 50px;
    border: none;
    &:focus {
      outline: none;
    }
  }
}
.toolbar__color {
  span {
    font-size: 20px;
    margin: 0 10px;
  }
}
.toolbar__tool {
  .active {
    background-color: #d8d8d8;
  }
}
.color {
  width: 50px;
  height: 50px;
  border-radius: 50px;
  display: inline-block;
  margin: 0 5px;
  vertical-align: middle;
  position: relative;
  cursor: pointer;
  &.active::after {
    content: "✓";
    display: block;
    position: absolute;
    top: 50%;
    left: 50%;
    transform: translate(-50%, -50%);
    height: 100px;
    text-align: center;
  }
  &.color-white {
    background-color: white;
    border: 1px solid black;
  }
  &.color-black {
    background-color: black;
    color: white;
  }
  &.color-lightgreen {
    background-color: #9bffcd;
  }
  &.color-green {
    background-color: #00cc99;
  }
  &.color-darkgreen {
    background-color: #01936f;
  }
}

.btn {
  position: absolute;
  text-align: center;
  vertical-align: middle;
  bottom: 0%;
  left: 50%;
  transform: translate(-50%, -50%);
  &:after {
    display: inline-block;
    height: 40px;
    width: 40px;
    line-height: 40px;
    background-color: white;
    border-radius: 50%;
    position: absolute;
    top: 50%;
    left: 50%;
    transform: translate(-50%, -50%);
    cursor: pointer;
  }
  &.down::after {
    content: "▼";
  }
  &.up::after {
    content: "▲";
  }
}
.btn-toolbar {
  position: fixed;
  left: 50%;
  bottom: 100px;
  transform: translateX(-50%);
}

.hideNavBar {
  top: -60px;
  & .btn::after {
    content: "▼";
    line-height: 55px;
  }
}
.hideToolBar {
  height: 0;
  li {
    display: none;
  }
  & .btn::after {
    content: "▲";
  }
}
</style>
