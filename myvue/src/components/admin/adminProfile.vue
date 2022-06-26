<template>



  <div class="root">
    <!--      侧边栏-->
  <el-container>
  <el-aside width="200px" style="z-index:1">
    <el-col :span="20">
      <el-menu
        default-active="1"
        class="el-menu-vertical-demo"
        background-color="Transparent"
        text-color="#000000"
        active-text-color="T#67C23A"
        :router="true">
        <!--侧边栏以index属性路由跳转-->
        <el-menu-item>
          <i class="el-icon-user"></i>
          <span @click="userManagementClick()">
            用户管理
            <span style="color:#B0E0E6;" v-show="userManagementShow" class="el-icon-s-tools"></span>
          </span>
        </el-menu-item>

<!--        <el-menu-item>-->
<!--          <i class="el-icon-document"></i>-->
<!--          <span @click="postManagementClick()">-->
<!--            文章管理-->
<!--            <span style="color:#B0E0E6;" v-show="postManagementShow" class="el-icon-s-tools"></span>-->
<!--          </span>-->
<!--        </el-menu-item>-->

        <el-submenu index="2">
          <template slot="title">
            帖子管理<span style="color:#B0E0E6;" v-show="postManagementShow" class="el-icon-s-tools"></span>
          </template>
          <el-menu-item v-for="item in this.$store.state.homepageClass" :key="item.typeId" @click="postManagementClick(item.typeId)">
            {{item.home}}
          </el-menu-item>
        </el-submenu>

      </el-menu>
    </el-col></el-aside>
  </el-container>

    <div class="managementPage" style="z-index:0">
<!--      用户管理页面-->
      <div class="userManagement" v-show="userManagementShow">
        11111111111
      </div>

<!--      帖子管理页面-->
      <div class="article" v-show="postManagementShow">
        <div class="articleItem" v-for="(item, index) in postsList" :key="index">
          <div class="post-block-code">
            <div class="post-code" v-html="'id:'+item.postId"></div>
            <div>
              <i class="el-icon-user"></i>
              发帖人: {{item.nickname}}
            </div>
          </div>
          <div class="ItemCenter">
            <div class="title">{{ item.title }}</div>
            <div class="publishDate">
              {{ item.updateTime}}
            </div>
            <div class="replyCount">
              <i class="iconfont icon-kuaisuhuifu"></i>
              楼层数:{{ item.floors }}
            </div>
          </div>
          <div class="ItemRight">
            <div>
              <el-button style="font-size:5px;color:#254ef5;"
                         @click="$router.push({ name: 'posts', params: { postsId: item.postId } })">查看
              </el-button>
            </div>
            <div>
              <el-button style="font-size:5px;color:#fd0707;"
                         @click="deletePost(item.postId)">删除
              </el-button>
            </div>
          </div>
        </div>
      </div>



      <div class="bottom">
        <!-- 分页组件 -->
        <el-pagination background layout="prev, pager, next" :total="100"
                       :current-page="this.$route.query.page * 1"
                       @current-change="changePage"
        >
        </el-pagination>
      </div>
      <GoTop></GoTop>
      </div>
    </div>



</template>

<script>
import GoTop from "../utils/GoTop";

export default {
  name: "adminProfile",
  components:{
    GoTop
  },
  data() {
    return {
      userManagementShow:true,
      postManagementShow:false,
      //文章管理相关
      postsList:[],
      category:1,
      page:1,
      eachPage:'',
      pagination:'',
      order:'',
      total:'',
      //用户管理相关

    }
  },


  methods:{
    userManagementClick(){
      this.userManagementShow=true;
      this.postManagementShow=false;
    },
    postManagementClick(id){
      this.postManagementShow=true;
      this.userManagementShow=false;
      this.category=id;
      this.page=1;
      this.getPosts();
    },


    //===================文章管理相关================


    //获取帖子列表
    getPosts() {
      const self = this;

      self.$axios({
        method:'get',
        url:'/post/posts?keyword=&userId&category='+this.category +'&size=3&page='+this.page+'&order=1'
      }).then(res=>{
        if(res.data.flag===true) {
          //console.log('/post/posts?keyword=&userId&category='+this.category +'&size=15&page='+this.page+'&order=1')
          // alert(res.data.message)
          this.postsList=res.data.data.records
          this.total=res.data.data.total
          console.log(res)
          window.scrollTo({
            top: 0,
            behavior: "smooth",
          });
        }
        else {
          console.log(res)
          alert(res.data.message)
        }
      })
    },
    //删除帖子
    deletePost(postId){
      let pid=postId
      const self = this
      self.$axios({
        method: 'delete',
        url: 'post/' + pid
      })
        .then(res => {
          if (res.data.flag === true) {
            alert("删除帖子成功")
            console.log(res)
            // this.$router.push('/homepageone?typeId='+"1"+'&page=1');
          } else {
            console.log(res)
            alert(res.data.message)
          }
        })
    },
    // 切换分页的回调
    changePage(e) {
      // console.log(e);
      // this.currentPage = e;
      this.page=e
      this.getPosts()
      // if (this.$route.query.typeId == 0) {
      //   // 查询全部文章
      //   this.getAllArticle();
      // } else {
      //   this.getArticleById(this.$route.query.typeId);
      // }
    }
  }
}
</script>

<style scoped>
.communityContainer {
  display: flex;
  justify-content: center;
  font-size: 15px;
}

.community {
  display: flex;
  max-width: 1200px;
  width: 85vw;
}

.left {
  position: sticky;
  top: 74px;
  padding: 20px 0;
  width: 200px;
  /* 74px+padding上下的20px */
  height: calc(100vh - 114px);
  color: rgb(83, 83, 83);
  font-size: 13px;
}

.right {
  padding: 10px 20px 20px;
  width: calc(100% - 200px);
}

.writeButton {
  width: 100%;
  margin-bottom: 10px;
}

.sortItem {
  margin: 15px 0;
  cursor: pointer;
}

.currentItem {
  color: #18365b;
  font-weight: 600;
}

.root{
  display: flex;
  position: relative;
}

.article {
  position: relative;
  padding: 20px 180px 0px 180px;
}

/*.articleItem {*/
/*  display: flex;*/
/*  position: relative;*/
/*  border-bottom: 1px solid #eee;*/
/*  padding: 20px 180px 20px;*/
/*  cursor: pointer;*/
/*  border-radius: 5px;*/
/*  border: solid;*/
/*  transition: all 0.15s;*/
/*}*/
.articleItem {
  display: flex;
  position: relative;
  border-bottom: 1px solid #eee;
  padding: 20px 20px 20px;
  cursor: pointer;
  border-radius: 5px;
  transition: all 0.15s;
}

.articleItem:hover {
  background-color: #f2f6fb;
}

.userAvatar {
  width: 45px;
}

.userAvatar img {
  height: 40px;
  width: 40px;
  border-radius: 50%;
}

.ItemCenter {
  width: 700px;
  margin: 0 10px;
}

.ItemCenter div {
  margin-bottom: 1px;
  line-height: 18px;
}

.title {
  color: rgb(43, 43, 43);
  white-space: nowrap;
  overflow: hidden;
  text-overflow: ellipsis;
}

.publishDate {
  color: rgb(136, 136, 136);
  margin: 4px 0;
  font-size: 12px;
}

.content {
  color: rgb(136, 136, 136);
  font-size: 14px;
  line-height: 19px;

  overflow: hidden;
  text-overflow: ellipsis;
  display: -webkit-box;
  -webkit-line-clamp: 2;
  line-clamp: 2;
  -webkit-box-orient: vertical;
}

.articleImg {
  margin: 15px 0 10px;
  display: flex;
  justify-content: flex-start;
}

.articleImg img {
  border-radius: 5px;
}

.articleImgItem {
  height: 100px;
  width: auto;
  margin-right: 10px;
}



.articleImgItem /deep/ .el-image__inner {
  width: unset;
}

.ItemRight {
  width: 45px;
  font-size: 14px;
  color: rgb(83, 83, 83);
  position: absolute;
  right: 30px;
}

.tips {
  width: 100%;
  text-align: center;
  font-size: 14px;
  color: rgb(158, 158, 158);
  margin: 20px 0;
}

.bottom {
  width: 100%;
  text-align: center;
  margin: 40px 0;
}

.communityContainer /deep/ .el-loading-spinner {
  margin-top: 80px;
}

.nullTips {
  text-align: center;
  margin-top: 20vh;
  color: #666;
  letter-spacing: 1px;
}
</style>
