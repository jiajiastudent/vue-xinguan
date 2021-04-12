<template>
    <div>
        <!-- 面包导航 -->
        <el-breadcrumb separator="/" style="padding-left:10px;padding-bottom:10px;font-size:12px;">
            <el-breadcrumb-item :to="{ path: '/home' }">首页</el-breadcrumb-item>
            <el-breadcrumb-item>其他管理</el-breadcrumb-item>
            <el-breadcrumb-item>个人博客</el-breadcrumb-item>
        </el-breadcrumb>
        <el-card>
            <iframe src="http://www.zykhome.club:8080" frameborder="no" border="0" marginwidth="0" marginheight="0"  allowtransparency="yes" style="overflow:hidden;" height="600px;" width="100%"></iframe>
        </el-card>
    </div>
</template>

