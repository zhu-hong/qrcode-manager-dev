<script>
export default {
  data() {
    return {
      profiles: {
        name: 'zhuhongaskjhk',
        avatar: 'https://ws-zh.vercel.app/avatar.webp',
      }
    }
  },
}
</script>

<template>
  <header class="header-color w-full h-16 px-6 flex justify-between items-center cursor-default children:(text-white flex justify-between items-center)">
    <div class="w-60 text-lg">
      <img src="../assets/header_logo.png" alt="header_logo">
      <i class="w-1px h-8 bg-gray-400"></i>
      <span>二维码</span>
    </div>
    <div class="w-72 children:(flex justify-between items-center) text-sm">
      <div>
        <span class="mr-2 w-20 h-8 leading-8 overflow-hidden overflow-ellipsis whitespace-normal">{{ profiles.name }}</span>
        <img :src="profiles.avatar" class="w-8 h-8 rounded-full border border-[2.5px]" alt="header_avatar">
      </div>
      <div>
        <span class="mr-2">帮助</span>
        <img src="../assets/header_help.png" alt="help">
      </div>
      <div>
        <span class="mr-2">退出</span>
        <img src="../assets/header_logout.png" alt="logout">
      </div>
    </div>
  </header>
</template>

<style>
.header-color {
  background-color: #223361;
}
</style>