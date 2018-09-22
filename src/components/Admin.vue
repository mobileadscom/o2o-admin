<template>
<div>
  <div class="campaignInfo">
    <div>Campaign: {{campaignName}}</div>
  </div>
  <div class="main-wrapper shadow-1">
    <div class="tab-container">
      <div @click="currentTab=1" :class="{active:currentTab == 1}">USER</div>
      <div @click="currentTab=2" :class="{active:currentTab == 2}">COUPON</div>
    </div>
    <div class="tab-content-wrapper">
      <transition-group name="tab-transition">
      <div class="tab-content" v-if="currentTab==1" :key="1">
        <div class="tab-content-title">GET USER INFO</div>
        <form @submit="getUser">
          <mini-input type="text" label="User Id" v-model="userId" id="userId" maxlength="100" color="#4285F4" fontColor="#444444" :error="userIdError"></mini-input>
          <button class="primary" type="submit" v-if="!userSubmitting" style="padding: 8px 20px;margin-left: 20px;">SUBMIT</button>
          <div class="form-loader" v-if="userSubmitting"></div>
          <div class="user-info-wrapper">
            <div class="queryMsg" :class={error:userQueryError} v-html="userQueryMsg"></div>
            <div v-for="(val, key) in userInfo" :key="key" class="user-info shadow-1">
              <div class="user-info-property">User Id: {{val.id}}</div>
              <div class="user-info-property">Coupon Code: {{val.couponCode}}</div>
              <div class="user-info-property">source: {{val.source}}</div>
              <div class="user-info-property" style="margin-bottom: 0px;">state: {{val.state}}</div>
            </div>
          </div>
        </form>
        
      </div>
      <div class="tab-content" v-if="currentTab==2" :key="2">
        <div class="tab-content-title">GET COUPON INFO</div>
      </div>
    </transition-group>
    </div>
  </div>
</div>
</template>

<script>
import axios from 'axios';
import MiniInput from '@/components/inputs/MiniInput';

export default {
  components: {
    MiniInput
  },
  data() {
    return {
      campaignName: 'BodyMainte',
      functionsDomain: 'https://us-central1-familymarto2o.cloudfunctions.net/twitter',
      apiDomain: 'https://api.mobileads.com',
      currentTab: 1,
      userId: '',
      userSubmitting: false,
      userIdError: '',
      userQueryMsg: '',
      userQueryError: false,
      userInfo: []
    }
  },
  methods: {
    getUserError() {
      this.userSubmitting = false;
      this.userQueryMsg = 'Error getting user info';
      this.userQueryError = true;
    },
    getUser2(userId) {
      return new Promise ((resolve, reject) => {
        axios.get(this.apiDomain + '/coupons/o2o/check_user', {
          params: {
            id: userId
          }
        }).then((response) => {
          console.log(response);
          this.userSubmitting = false;
          if (response.data.user.length > 0) {
            this.userInfo = response.data.user;
            this.userQueryMsg = 'User Found!';
            this.userQueryError = false;
            resolve(this.userInfo);
          }
          else {
            this.userInfo = [];
            this.userQueryMsg = 'User Not Found';
            this.userQueryError = true;
            resolve([]);
          }
        }).catch((error) => {
          this.getUserError();
          console.error(error);
          reject(error);
        });
      });
    },
    getUser(event) {
      event.preventDefault();
      if (this.userId) {
        this.userSubmitting = true;
        if (this.userId.indexOf('@') > 0) {
          // email
          console.log('email');
          this.getUser2(this.userId);
        }
        else {
          //twitter
          console.log('twitter');
          var isTwitterId = /^\d+$/.test(this.userId);
          if (!isTwitterId) {
            axios.post(this.functionsDomain + '/getTwitterId', {
              id: this.userId
            })
            .then((res) => {
              console.log(res);
              this.getUser2(res.data.id).then((response) => {
                if (response.length > 0) {
                  this.userQueryMsg = `User Found! <br>Twitter Name: ${res.data.screen_name}`;
                }
              });
            }).catch((err) => {
              console.error(error);
              this.getUserError();
            });
          }
          else {
            this.getUser2(this.userId).then((response) => {
              if (response.length > 0) {
                axios.post(this.functionsDomain + '/getTwitterName', {
                  id: response[0].id
                }).then((res) => {
                  this.userQueryMsg = `User Found! <br>Twitter Name: ${res.data.screen_name}`;
                })
              }
            }).catch((error) => {
              this.getUserError();
            });
          }
        }

      }
      else {
        this.userIdError = 'User Id is empty.'
      }
    }
  }
}
</script>

<style>
  .tab-transition-enter-active, .tab-transition-leave-active {
    transition: all 0.3s;
  }

  .tab-transition-leave-active {
    position: absolute;
  }

  .tab-transition-enter, .tab-transition-leave-to {
    transform: translateY(30px);
    opacity: 0;
  }

  .campaignInfo {
    margin: 25px 0px;
  }

  .main-wrapper {
    max-width: 600px;
    margin: auto;
  }

  .tab-container {
    display: flex;
    justify-content: space-around;
  }

  .tab-container div {
    padding: 10px;
    width: 50%;
    text-align: center;
    font-weight: 600;
    color: #888;
    position: relative;
    transition: all 0.3s;
  }

  .tab-container div::after {
    content: "";
    width: 96%;
    height: 4px;
    background: #e82f22;
    position: absolute;
    bottom: 0px;
    left: 2%;
    z-index: 2;
    transition: all 0.3s;
    transform: scaleX(0);
  }

  .tab-container div:hover, .tab-container div.active{
    cursor: pointer;
    color: #e82f22;
  }

  .tab-container div:hover::after, .tab-container div.active::after {
    transform: scaleX(1);
  }

  .tab-content-wrapper {
    border-top: 1px solid #aaa;
  }

  .tab-content {
    padding: 20px 10px;
  }

  .tab-content-title {
    margin-left: 20px;
    font-size: 18px;
    font-weight: 600;
    color:#666;
    margin-bottom: 20px;
  }
 
  .queryMsg {
    font-weight: 600;
    color: #4285F4;
    margin-left: 5px;
    margin-bottom: 10px;
  }

  .queryMsg.error {
    color: red;
  }

  .user-info-wrapper {
    margin: 15px;
  }

  .user-info {
    margin: 5px 5px 15px;
    padding: 10px;
    background-color: #fafafa;
  }

  .user-info-property {
    margin-bottom: 5px;
  }
</style>

