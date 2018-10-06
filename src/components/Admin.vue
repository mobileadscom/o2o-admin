<template>
<div style="padding-top: 30px;">
  <!-- <div class="campaignInfo">
    <div>Campaign: {{campaignName}}</div>
  </div> -->
  <div class="main-wrapper shadow-1">
    <div class="tab-container">
      <div @click="currentTab=1" :class="{active:currentTab == 1}">{{wording.USER[lang]}}</div>
      <div @click="currentTab=2" :class="{active:currentTab == 2}">{{wording.COUPON[lang]}}</div>
    </div>
    <div class="tab-content-wrapper">
      <transition-group name="tab-transition">
      <div class="tab-content" v-if="currentTab==1" :key="1">
        <div class="tab-content-title">{{wording.getUserInfo[lang]}}</div>
        <form @submit="getUser">
          <mini-input type="text" :label="wording.twitterID[lang]" v-model="userId" id="userId" maxlength="100" color="#4285F4" fontColor="#444444" :error="userIdError"></mini-input>
          <button class="primary" type="submit" v-if="!userSubmitting" style="padding: 8px 20px;margin-left: 20px;">{{wording.SUBMIT[lang]}}</button>
          <div class="form-loader" v-if="userSubmitting"></div>
          <div class="user-info-wrapper">
            <div class="queryMsg" :class={error:userQueryError} v-html="userQueryMsg"></div>
            <div v-for="(val, key) in userInfo" :key="key" class="user-info shadow-1">
              <div class="user-info-property">Twitter Id: {{val.id}}</div>
              <div class="user-info-property">{{wording.registeredOn[lang]}}: {{val.formattedDate}}</div>
              <div class="user-info-property" :class="{reissued: val.reissued}">{{wording.couponCode[lang]}}: <span v-html="val.couponLink"></span></div>
              <div class="user-info-property">{{wording.source[lang]}}: {{val.source}}</div>
              <div class="user-info-property">{{wording.state[lang]}}: {{val.state}}</div>
              <div class="secondary" v-if="val.state == 'win' && !val.reissued && !val.reissuing" @click="reissueCoupon(key)" style="margin: 4px 0px;">{{wording.reissueCoupon[lang]}}</div>
              <div class="small-loader" v-if="val.reissuing" style="margin: 4px 0px;"></div>
              <div class="user-info-property" v-if="val.reissued" v-html="val.reissueResult">
              </div>
            </div>
          </div>
        </form>
        
      </div>
      <div class="tab-content" v-if="currentTab==2" :key="2">
        <div class="tab-content-title">{{wording.getCouponInfo[lang]}}</div>
        <form @submit="getCoupon">
          <mini-input type="text" :label="wording.couponCode[lang]" v-model="couponCode" id="couponCode" maxlength="100" color="#4285F4" fontColor="#444444" :error="couponCodeError"></mini-input>
          <button class="primary" type="submit" v-if="!couponSubmitting" style="padding: 8px 20px;margin-left: 20px;">{{wording.SUBMIT[lang]}}</button>
          <div class="form-loader" v-if="couponSubmitting"></div>
        </form>
        <div class="coupon-info-wrapper">
          <div class="queryMsg" :class={error:couponQueryError} v-html="couponQueryMsg"></div>
          <div class="coupon-info-property-wrapper">
            <div class="coupon-info shadow-1" v-if="couponInfo.group">
              <div class="coupon-info-property">{{wording.claimed[lang]}}: {{couponInfo.claimed}}</div>
              <div class="coupon-info-property">{{wording.group[lang]}}: {{couponInfo.group}}</div>
              <div class="coupon-info-property">{{wording.owner[lang]}}: {{couponInfo.owner}}</div>
              <div class="coupon-info-property">{{wording.issued[lang]}}: {{couponInfo.redeemed}}</div>
              <div class="coupon-info-property">{{wording.issuedOn[lang]}}: {{couponInfo.formattedDate}}</div>
              <div v-if="couponUserInfo.length > 0">
                <div class="coupon-info-property" style="margin-bottom: -2px; padding-bottom: 0px;">{{wording.ownerInfo[lang]}}:</div>
                <div class="user-info-wrapper">
                  <div v-for="(val, key) in couponUserInfo" :key="key" class="user-info shadow-1">
                    <div class="user-info-property">Twitter Id: {{val.id}}</div>
                    <div class="user-info-property">{{wording.registeredOn[lang]}}: {{val.formattedDate}}</div>
                    <div class="user-info-property" :id="'couponTwitter' + key">{{wording.twitterName[lang]}}: {{val.twitterName}}</div>
                    <div class="user-info-property">{{wording.couponCode[lang]}}: <span v-html="val.couponLink"></span></div>
                    <div class="user-info-property">{{wording.source[lang]}}: {{val.source}}</div>
                    <div class="user-info-property">{{wording.state[lang]}}: {{val.state}}</div>
                  </div>
                </div>
              </div>
            </div>
          </div>
        </div>
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
      params: [],
      wording: {
        'USER': {
          'en': 'USER',
          'ja': 'ユーザー'
        },
        'COUPON': {
          'en': 'COUPON',
          'ja': 'クーポン'
        },
        'getUserInfo': {
          'en': 'Get User Info',
          'ja': 'ユーザー情報を確認'
        },
        'getCouponInfo': {
          'en': 'Get Coupon Info',
          'ja': 'クーポン情報を確認'
        },
        'SUBMIT': {
          'en': 'SUBMIT',
          'ja': '確認'
        },
        'twitterID': {
          'en': 'Twitter ID',
          'ja': 'Twitter ID'
        },
        'twitterName': {
          'en': 'Twitter Name',
          'ja': 'Twitter 名'
        },
        'registeredOn': {
          'en': 'Registered On',
          'ja': '登録日時'
        },
        'couponCode': {
          'en': 'Coupon Code',
          'ja': 'クーポンコード'
        },
        'source': {
          'en': 'Source',
          'ja': '店舗'
        },
        'state': {
          'en': 'State',
          'ja': '状態'
        },
        'reissueCoupon': {
          'en': 'Reissue Coupon',
          'ja': 'クーポン再発行'
        },
        'claimed': {
          'en': 'Claimed',
          'ja': 'クーポン交換'
        },
        'group': {
          'en': 'Group',
          'ja': '店舗'
        },
        'owner': {
          'en': 'Owner',
          'ja': '保有しているユーザー'
        },
        'issued': {
          'en': 'Issued',
          'ja': '発行済み'
        },
        'issuedOn': {
          'en': 'Issued On',
          'ja': '発行日時'
        },
        'ownerInfo': {
          'en': 'Owner Info',
          'ja': '所有者情報'
        },
        'win': {
          'en': 'win',
          'ja': '当選'
        },
        'lose': {
          'en': 'lose',
          'ja': '落選者'
        },
        'FamilyMart': {
          'en': 'Family Mart',
          'ja': 'ファミリーマート'
        },
        'CircleK': {
          'en': 'Circle K',
          'ja': 'CKS'
        },
        'exchanged': {
          'en': 'True',
          'ja': '交換済み'
        },
        'notExchanged': {
          'en': 'False',
          'ja': '未交換'
        },
      },
      lang: 'ja',
      campaignName: 'BodyMainte',
      functionsDomain: 'https://us-central1-familymarto2o.cloudfunctions.net/twitter',
      apiDomain: 'https://api.mobileads.com',
      currentTab: 1,
      campaignId:'634501954374a87c6b6f4dde00493ded',
      adUserId: '4831',
      rmaId: '3',
      generalUrl: 'https://track.richmediaads.com/a/analytic.htm?rmaId={{rmaId}}&domainId=0&pageLoadId={{cb}}&userId={{adUserId}}&pubUserId=0&campaignId={{campaignId}}&callback=trackSuccess&type={{type}}&value={{value}}&uniqueId={{userId}}&customId={{source}}',
      timestamp: Date.now(),
      userId: '',
      userSubmitting: false,
      userIdError: '',
      userQueryMsg: '',
      userQueryError: false,
      userInfo: [],
      couponCode: '',
      couponCodeError: '',
      couponSubmitting: false,
      couponQueryMsg: '',
      couponQueryError: false,
      couponInfo: {},
      couponUserInfo: []
    }
  },
  methods: {
      getParams() {
        var query_string = {};
        var query = window.location.search.substring(1);
        var vars = query.split("&");
        for (var i=0;i<vars.length;i++) {
            var pair = vars[i].split("=");
            // If first entry with this name
            if (typeof query_string[pair[0]] === "undefined") {
                query_string[pair[0]] = pair[1];
            // If second entry with this name
            } else if (typeof query_string[pair[0]] === "string") {
                var arr = [ query_string[pair[0]], pair[1] ];
                query_string[pair[0]] = arr;
            // If third or later entry with this name
            } else {
                query_string[pair[0]].push(pair[1]);
            }
        } 
        return query_string;
    },
    formatDate(time) {
      var months = ["January", "February", "March", "April", "May", "June", "July", "August", "September", "October", "November", "December"];
      var date = new Date(time);
      var day = (date.getDate()).toString();
      var month = months[date.getMonth()].toString();
      var year = (date.getFullYear()).toString();
      var hours = (date.getHours()).toString();
      var minutes = (date.getMinutes()).toString();
      minutes = minutes.length < 2 ? '0' + minutes : minutes;
      return `${day}-${month}-${year} ${hours}:${minutes}`;
    },
    trackReissue(userId, oldCoupon, newCoupon, source) {
      var trackingUrl = this.generalUrl.replace('{{rmaId}}', this.rmaId).replace('{{campaignId}}', this.campaignId).replace('{{adUserId}}', this.adUserId).replace('{{cb}}', this.timestamp.toString());
      var type = 'coupon_reissue';
      var value = `${userId}_${oldCoupon}_${newCoupon}`;
      var url = trackingUrl.replace('{{type}}', type).replace('{{value}}', value).replace('{{userId}}', userId).replace('{{source}}', source);
      return axios.get(url);
    },
    getUserError() {
      this.userSubmitting = false;
      this.userQueryMsg = 'Error getting user info';
      this.userQueryError = true;
    },
    translateResult(data) {
      var translatedVariable = ['source', 'state', 'group', 'claimed']
      for (var d in data) {
        if (translatedVariable.indexOf(d) > -1 ) {
          data[d] = this.wording[data[d]][this.lang];
        }
      }
      return data;
    },
    getUser2(userId) {
      return new Promise ((resolve, reject) => {
        axios.get(this.apiDomain + '/coupons/o2o/check_user', {
          params: {
            id: userId
          }
        }).then((response) => {
          this.userSubmitting = false;
          if (response.data.user.length > 0) {
            for (var r = 0; r < response.data.user.length; r++) {
              response.data.user[r].reissuing = false;
              response.data.user[r].reissued = false;
              response.data.user[r].reissueResult = '';
              if (response.data.user[r].state == 'win') {
                var couponLink = this.generateCouponLink(response.data.user[r].id, response.data.user[r].source);
                response.data.user[r].couponLink = `<a href="${couponLink}" target="_blank"> ${response.data.user[r].couponCode}</a>`;
              }
              else {
                response.data.user[r].couponLink = '';
              }
              response.data.user[r].formattedDate = this.formatDate(response.data.user[r].dateCreated);
              response.data.user[r] = this.translateResult(response.data.user[r]);
            }
            this.userInfo = response.data.user;
            if (this.lang == 'ja') {
              this.userQueryMsg = 'ユーザーが存在しています';
            }
            else{
              this.userQueryMsg = 'User Found!';
            }
            this.userQueryError = false;
            resolve(this.userInfo);
          }
          else {
            this.userInfo = [];
            if (this.lang == 'ja') {
              this.userQueryMsg = 'ユーザーが見つかりません';
            }
            else {
              this.userQueryMsg = 'User Not Found';
            }
            
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
        this.userIdError = '';
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
            if (this.userId.indexOf('@') > -1) {
              this.userId = this.userId.replace('@', '');
            }
            axios.post(this.functionsDomain + '/getTwitterId', {
              id: this.userId
            })
            .then((res) => {
              this.getUser2(res.data.id).then((response) => {
                if (response.length > 0) {
                  if (this.lang == 'ja') {
                    this.userQueryMsg = `ユーザーが存在しています <br>Twitter 名: ${res.data.screen_name}`;
                  }
                  else {
                    this.userQueryMsg = `User Found! <br>Twitter Name: ${res.data.screen_name}`;
                  }
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
                  if (this.lang == 'ja') {
                    this.userQueryMsg = `ユーザーが存在しています <br>Twitter 名: ${res.data.screen_name}`;
                  }
                  else {
                    this.userQueryMsg = `User Found! <br>Twitter Name: ${res.data.screen_name}`;
                  }
                })
              }
            }).catch((error) => {
              this.getUserError();
            });
          }
        }

      }
      else {
        this.userIdError = 'Input is empty.'
      }
    },
    generateCouponLink(userId, source) {
      return 'https://couponcampaign.predelistyle.com/%E3%83%9C%E3%83%87%E3%82%A3%E3%83%A1%E3%83%B3%E3%83%86%E3%83%89%E3%83%AA%E3%83%B3%E3%82%AF/coupon.html?userId=' + userId + '&source=' + source;
    },
    reissueCoupon(k) {
      this.userInfo[k].reissuing = true;
      var userId = this.userInfo[k].id;
      var source = this.userInfo[k].source;
      var group = this.userInfo[k].source == 'CircleK' ? 'B' : 'A';
      axios.post(this.apiDomain + `/coupons/o2o/reissue?id=${userId}&source=${source}&group=${group}`).then((response) => {
        if (response.data.status) {
          this.userInfo[k].reissuing = false;
          this.userInfo[k].reissued = true;
          this.userInfo[k].reissueResult = 'Coupon Reissued!';
          this.trackReissue(userId, this.userInfo[k].couponCode, response.data.newCouponCode, source);
          var couponLink = this.generateCouponLink(this.userInfo[k].id, this.userInfo[k].source);
          this.userInfo[k].couponLink = `<a href="${couponLink}" target="_blank"> ${response.data.newCouponCode}</a>`;
          this.userInfo[k].couponCode = response.data.newCouponCode;
        }
        else {
          this.userInfo[k].reissuing = false;
        }
      }).catch((error) => {
        console.error(error);
        this.userInfo[k].reissuing = false;
      })
    },
    getCoupon(event) {
      event.preventDefault();
      if (this.couponCode) {
        this.couponCodeError = '';
        axios.get(this.apiDomain + '/coupons/o2o/check_coupon', {
          params: {
            couponCode: this.couponCode
          }
        }).then((response) => {
          if (response.data.coupon) {
            this.couponQueryError = false;
            if (this.lang == 'ja') {
              this.couponQueryMsg = 'クーポンが存在しています';
            }
            else {
              this.couponQueryMsg = 'Coupon Found!';
            }
            if (!response.data.coupon.claimed) {
              response.data.coupon.claimed = false;
            }
            response.data.coupon.claimed = response.data.coupon.claimed ? 'exchanged' : 'notExchanged';
            response.data.coupon.redeemed = response.data.coupon.redeemed ? 'Yes' : 'No';
            if (response.data.coupon.group == 'A') {
              response.data.coupon.group = 'FamilyMart';
            }
            if (response.data.coupon.group == 'B') {
              response.data.coupon.group = 'CircleK'
            }
            
            if (response.data.coupon.date) {
              response.data.coupon.formattedDate = this.formatDate(response.data.coupon.date);
            }
            
            // console.log(response.data.coupon);
            this.translateResult(response.data.coupon);
            this.couponInfo = Object.assign({}, response.data.coupon);
            if (response.data.user.length > 0) {
              for (var u = 0; u < response.data.user.length; u++) {
                response.data.user[u].twitterName = 'loading...';
                if (response.data.user[u].state == 'win') {
                  var couponLink = this.generateCouponLink(response.data.user[u].id, response.data.user[u].source);
                  response.data.user[u].couponLink = `<a href="${couponLink}" target="_blank"> ${response.data.user[u].couponCode}</a>`;
                }
                else {
                  response.data.user[u].couponLink = '';
                }
                response.data.user[u] = this.translateResult(response.data.user[u]);
                response.data.user[u].formattedDate = this.formatDate(response.data.user[u].dateCreated);
              }
              this.couponUserInfo = response.data.user;
              axios.post(this.functionsDomain + '/getTwitterName', {
                id: this.couponUserInfo[0].id
              }).then((res) => {
                for (var i = 0; i < response.data.user.length; i++) {
                  this.couponUserInfo[i].twitterName = res.data.screen_name;
                  if (this.lang == 'ja') {
                    document.getElementById('couponTwitter' + i.toString()).innerHTML = 'Twitter 名: ' + res.data.screen_name;
                  }
                  else{
                    document.getElementById('couponTwitter' + i.toString()).innerHTML = 'Twitter Name: ' + res.data.screen_name;
                  }
                  
                }
              }).catch((err) => {
                console.error(err);
              })

            }
            else {
              this.couponUserInfo = [];
            }
          }
          else {
            this.couponInfo = {};
            this.couponQueryError = true;
            if (this.lang == 'ja') {
              this.couponQueryMsg = 'クーポンは見つかりません';
            }
            else {
              this.couponQueryMsg = 'Coupon Not Found';
            }
            
          }
        }).catch((error) => {
          console.error(error);
        })
      }
      else {
        this.couponCodeError = 'Input is empty.'
      }
    }
  },
  mounted() {
    this.params = this.getParams();
    console.log(this.params);
    if (this.params.lang) {
      this.lang = this.params.lang == 'en' ? 'en' : 'ja' ;
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

  .user-info-wrapper, .coupon-info-wrapper {
    margin: 15px;
  }

  .user-info, .coupon-info {
    margin: 5px 5px 15px;
    padding: 4px 10px;
    background-color: #fafafa;
  }

  .user-info-property, .coupon-info-property {
    padding: 2px;
    transition: all 0.3s;
  }
/*.user-info-property:nth-child(odd), .coupon-info-property:nth-child(odd) {
  background-color: #eee;
}

.user-info-property:nth-child(even), .coupon-info-property:nth-child(even) {
  background-color: #ddd;
}*/
  .coupon-info .user-info {
    background-color: white;
  }

  .reissued {
    background-color: #7fff00;
  }
</style>

