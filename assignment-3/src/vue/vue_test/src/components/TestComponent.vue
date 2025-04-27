<template>
<div class="login-box" v-for="(item, index) in userBoxes" :key="item" >
    <div class="box-head">
        <h3>user - {{ index }}</h3>
        <button type="button" class="close-btn" @click="removeUserBox(index)">X</button>
    </div>
    <div class="insert-box" >
        <label for="name">Name</label>
        <input v-model="item.name" type="text" id="name" name="name" >
        <div class="error-msg" >{{ item.errMsg.name }}</div>
        
    </div>
    <div class="insert-box">
        <label for="password">Password</label>
        <input v-model="item.password" type="password" id="password" name="password">
        <div class="error-msg"  >{{ item.errMsg.password }}</div>
    </div>
</div>
<div class="box-footer">
    <button type="button" class="footer-btn" @click = addUserBox >Add user</button>
    <button type="submit"  :class="['footer-btn', { 'isActive' : !isEmpty }]"  @click="getUserInfo">Confirm</button>
</div>
<div >
    <div v-for="item in userBoxes" :key="item" class="user-info-box" v-show="openInfo">
        <div class="user-info-item">
            <span class="get-title">Name: </span>
            <span> {{ item.name }}</span>
        </div>
        <div class="user-info-item">
            <span class="get-title">Password:</span>
            <span>{{ encryption(item.password) }}</span>
        </div>
    </div>

</div>

</template>

<script setup>
import {  computed, onMounted, reactive,  ref,  watch } from 'vue';

const openInfo = ref(false);

const addUserBox = () => {
    openInfo.value = false;
    userBoxes.push({
        name : ''
        ,password : ''
        ,errMsg: {
            name : ''
            ,password : ''
        }
    })

}


const userBoxes = reactive([])
onMounted(() => {
  addUserBox() 
})

watch(
  () => userBoxes.map(user => ({ name: user.name, password: user.password })),
  () => {
    userBoxes.forEach((user) => {
        console.log(userBoxes);
    // Name 확인
    if(user.name.length === 0) {
        user.errMsg.name = '';
    }
    else if(user.name.length < 3) {
        user.errMsg.name = 'Name must be at least 3 charters long.';
    }
    // 중복 Name 확인
    else if(userBoxes.some(chk => chk !== user && chk.name === user.name)) {
        user.errMsg.name = `The name '${user.name}' is duplicated.`
        
    }
    else {
        user.errMsg.name = '';
    }

    // password 확인
    if(user.name) {
        if(!user.password) {
            user.errMsg.password = 'Password is required.';
        }
        else if(user.password.length < 6) {
            user.errMsg.password = 'Password must be at least 6 charcters long.';
        }
    
        else {
            user.errMsg.password = '';
        }
    }
    openInfo.value = false; // input 내용 변경 감지 되면 user-info-box닫기
    });
})


// confirm 클래스 적용
const isEmpty = computed(() => {
    return userBoxes.every(chk => 
        chk.name.length >= 3
        && chk.password.length >= 6
        &&!userBoxes.some(u => u !== chk && u.name === chk.name)
)
});

// confirm 유저 정보 저장
const getUserInfo = () => {
    if(userBoxes) {
        openInfo.value = true;
    }
}

//비밀번호 암호화 처리
const encryption = (password) => {
    const three = password.substring(0, 3); // 앞 3자리 표시
    const masking = '*'.repeat(password.length).substring(3); //나머지 *
    return three + masking;
}



// x 클릭하면 삭제
const removeUserBox = (index) => {
    if(index.length != 1) {
        userBoxes.splice(index, 1)
    }
    
}




</script>

<style scoped>
/* 공통 */
* {
    margin: 0;
    padding: 0;
    box-sizing: border-box;
}

button {
    background-color: #ffffff;
    border: 1px solid #000;
    padding: 5px;
    cursor: pointer;
}

.error-msg {
    font-size: 0.6rem;
    color: #ff0000;
}

/* 전체 박스 테두리 */
.login-box {
    margin-bottom: 20px;
    border: 1px solid #000;
    width: 500px;
    /* height: 280px; */
}

.box-head {
    display: flex;
    justify-content: space-between;
    padding: 10px;
}

.box-footer {
    display: flex;
    gap: 5px;
    margin-bottom: 15px;
}

.footer-btn {
    background-color: #ffffff;
    border: 1px solid #000;
    padding: 5px;
    cursor: pointer;
    width: 88px;
}

.isActive {
    background-color: #dddddd;
    color: #969696;
    border: #969696 1px solid;
    cursor: auto;
    width: 88px;
}



/* 입력창 */
.insert-box {
    display: flex;
    flex-direction: column;
    padding: 10px;
}

input {
    height: 35px;
    padding: 5px;
}

input:focus {
    border: 2px solid #ff0000;
    outline: none;
    background-color: #fff4f4;
}

label {
    font-size: 0.8rem;
}

/* 저장된 유저정보 */
.user-info-box {
    background-color: #f3f3f3;
    width: 500px;
    padding: 15px;
    font-size: 0.9rem;
    
}

.user-info-item {
    display: flex;
}

.get-title {
    font-weight: 700;
    padding-right: 5px;
}



</style>
