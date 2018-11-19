let imgsrc = [];
$(function () {
    let flag = "",tim = "",sure = "";
    let code = "",indx = 0,it = {},its = {};
    let every = [],inventory = [],need = [],visa = [];
    //1.加密 
    var base = new Base64(); 
    // 总结
    let main = {}
    var vm = new Vue({
        el: ".container",
        data: {
            // 初始化
            arrlist: [],
            msg:"",
            baseurl: "http://101.200.83.32:8213/goldTest/api/v1",
            // baseurl: "http://101.200.83.32:8113/gold2/api/v1",
            team_number: "",
            // 出行须知
            need: [],
            need_no: [],
            // 签证信息
            visa: [],
            visas:[],
            // 物品清单
            thingList: [],
            sonList: [],
            allson: [],
            ind: 0,
            single: [],
            ss: [],
            // 行程信息
            suggestDate: [],
            info1:[],
            info2:[],
            info3:[],
            info4:[],
            info5:[],
            allss:{},
            item:"",
            arrangeht:"",
            //总提交
            post:{},
            num:0
        },
        mounted() {
            this.acquire();
        },
        methods: {
            // 初始化
            acquire() {
                // getcookie('user_id');
                let info = { page: 0, rows: 10, query: { userId: "14", teamName: "" } };
                // let info = { page: 0, rows: 1000000000, query: { user_id: getcookie('user_id'), team_name: "" }};
                var that = this;
                $.ajax({
                    type: "post",
                    dataType: "json",
                    url: that.baseurl + "/newTrip/findAllNewTrip",
                    data: JSON.stringify(info),
                    headers: {
                        // token:getcookie('token')
                        token: "bO7Rj6PsPA2QbEhNuYt12f2C0eqQBnS+exmDK2ejcNmPJKCX9OEcIGHkuLk8QM9MB/A/KdXFGEyzFpGm8T1BeA=="
                    },
                    success: function (data) {
                        console.log(data)
                        that.arrlist = data.data.sendData;
                    },
                    error: function (err) {
                        console.log(err)
                    }
                })
            },
            //获取组名
            getname(e, a) {
                $("#needs,#edit").children().remove();
                sessionStorage.clear();
                inventory = [];
                every = [];
                need = [];
                visa = [];
                this.sonList = [];
                this.allson = [];
                this.single = [];
                this.ss = [];
                $(".list .father span").css({
                    background: "white",
                    color: "black"
                })
                $(".right li").css("border","1px solid lightgray")
                // ------------------
                this.msg = e;
                $("#trip-cover-image").attr("src", "fcl/assets/images/up.png");
                this.team_number = a;
                let info = { team_number: a };
                // 总提交的 team_number等
                main.team_number = a;
                main.trip_name = e;
                main.create_user_id = getcookie('user_id');
                // ---------------
                var that = this;
                that.need = [];
                that.visa = [];
                $.ajax({
                    type: "post",
                    dataType: "json",
                    url: that.baseurl + "/yoosureAddress/findAddNewTripInfoByTeamNumber",
                    data: JSON.stringify(info),
                    headers: {
                        // token:getcookie('token')
                        token: "bO7Rj6PsPA2QbEhNuYt12f2C0eqQBnS+exmDK2ejcNmPJKCX9OEcIGHkuLk8QM9MB/A/KdXFGEyzFpGm8T1BeA=="
                    },
                    success: function (data) {
                        console.log(data)
                        $(".allname").hide()
                        let all = data.data.sendData.travelknow;
                        let allvisa = data.data.sendData.visa;
                        that.thingList = data.data.sendData.inventory;
                        that.suggestDate = data.data.sendData.suggestDate;
                        every.length = that.suggestDate.length;
                        all.forEach(e => {
                            if (e.travelknow == "") {
                                that.need.push(e)
                            }
                        });
                        allvisa.forEach(e => {
                            if (e.visa == "") {
                                that.visa.push(e)
                            }
                        });
                        that.suggestDate.forEach(e => {
                            that.allss[e] = {
                                time:"",
                                address:"",
                                job:"",
                                name:"",
                                tel:"",
                            }
                        });
                        
                    },
                    error: function (err) {
                        console.log(err)
                    }
                })
            },
            // 物品清单
            showSon(e) {
                var _this = e.currentTarget;
                $(".son").find("span").attr("flag","true")
                $(".son").find("span").css({
                    background: "white",
                    color: "black"
                })
                $(".list .father span").css({
                    background: "white",
                    color: "black"
                })
                $(_this).css({
                    background: "red",
                    color: "white"
                })
                let idx = $(_this).index();
                this.ind = idx;
                lisid = idx;
                this.sonList = this.thingList[idx].list;
                // console.log(this.sonList)
                this.allson = []
                // console.log(this.single[idx])
                if (this.single[idx]) {
                    this.sonList.forEach((e, index) => {
                        this.single[idx].forEach(item => {
                            if (e.type_name == item) {
                                $(".list .son span").eq(index).css({
                                    background: "red",
                                    color: "white"
                                })
                                $(".list .son span").eq(index).attr("flag","false")
                                this.allson.push(item)
                            }
                        });
                    });
                }
            },
            chooseSon(e) {
                var _this = e.currentTarget;
                let idx = $(_this).index()
                let col = $(_this).attr("flag");
                if(col == "true"){
                    $(_this).css({
                        background: "red",
                        color: "white"
                    })
                    this.allson.push(this.sonList[idx].type_name)
                    $(_this).attr("flag","false");
                }else if(col == "false"){
                    $(_this).css({
                        background: "white",
                        color: "black"
                    })
                    $(_this).attr("flag","true");
                    this.allson.splice(this.allson.findIndex(item => item === this.sonList[idx].type_name), 1)
                }
                this.single[this.ind] = this.allson;
                this.ss.push(this.single)
                this.ss = Array.from(new Set(this.ss))
                console.log(this.ss)
            },
            // 点击创建
            create(e,idd) {
                console.log(e)
                indx = idd;
                if(e.travelknow || e.travelknow == ""){
                    it = e;
                    flag = "create";
                    $('#summernote').summernote('code', e.travelknow);
                    $(".form-control").attr("onchange", "getPhoto(this.files)")
                }else{
                    its = e;
                    flag = "visa";
                    $('#summernote').summernote('code', e.travelknow);
                    $(".form-control").attr("onchange", "getPhoto(this.files)")
                }
                $('#summernote').summernote('code', visa);
                $(".form-control").attr("onchange", "getPhoto(this.files)")
                $(".edit").show();
                $("#masks").show();
            },
            // 点击编辑 以及保存
            edit(e) {
                var _this = e.currentTarget;
                let idxss = $(_this).parent().parent().parent().index();
                $(".time,.address,.job,.ne,.tel").find("input").css("border","1px solid lightgray");
                $(".arrange").children("div").find("li").attr("class","");
                if ($(_this).text() == "编辑") {
                    $(_this).text("保存");
                    $(_this).parent().children(".sonui_cancel").show();
                    $(_this).parent().parent().parent().find(".add_trvel").show();
                    $(".right li").eq(idxss).attr("class","mui-table-view-cell mui-collapse mui-active")
                    $(_this).parent().parent().parent().children(".mui-collapse-content").show()
                } else {
                    $(_this).text("编辑");
                    $(_this).parent().children(".sonui_cancel").hide();
                    $(_this).parent().parent().parent().find(".add_trvel").hide();
                    $(".right li").eq(idxss).attr("class","mui-table-view-cell mui-collapse");
                    $(_this).parent().parent().parent().children(".mui-collapse-content").hide()
                    let de = $(_this).parent().parent().parent().find(".times").text();
                    let contact = [];
                    let gather = [];
                    let kth = $(_this).parent().parent().parent().find(".time").children("input").length;
                    let kth2 = $(_this).parent().parent().parent().find(".address").children("input").length;
                    let kth3 = $(_this).parent().parent().parent().find(".job").children("input").length;
                    let kth4 = $(_this).parent().parent().parent().find(".ne").children("input").length;
                    let kth5 = $(_this).parent().parent().parent().find(".tel").children("input").length;
                    this.allss[de].time = [];
                    this.allss[de].address = [];
                    this.allss[de].job = [];
                    this.allss[de].name = [];
                    this.allss[de].tel = [];
                    for(var i = 0;i < kth;i++){
                        let vau = $(_this).parent().parent().parent().find(".time").children("input").eq(i).val();
                        this.allss[de].time.push(vau)
                    }
                    for(var i = 0;i < kth2;i++){
                        // let vau = $(".address input").eq(i).val();
                        let vau = $(_this).parent().parent().parent().find(".address").children("input").eq(i).val();
                        this.allss[de].address.push(vau)
                    }
                    for(var i = 0;i < kth3;i++){
                        // let vau = $(".job input").eq(i).val();
                        let vau = $(_this).parent().parent().parent().find(".job").children("input").eq(i).val();
                        this.allss[de].job.push(vau)
                    }
                    for(var i = 0;i < kth4;i++){
                        // let vau = $(".ne input").eq(i).val();
                        let vau = $(_this).parent().parent().parent().find(".ne").children("input").eq(i).val();
                        this.allss[de].name.push(vau)
                    }
                    for(var i = 0;i < kth5;i++){
                        // let vau = $(".tel input").eq(i).val();
                        let vau = $(_this).parent().parent().parent().find(".tel").children("input").eq(i).val();
                        this.allss[de].tel.push(vau)
                    }
                    let ln1 = this.allss[de].time.length;
                    let ln2 = this.allss[de].address.length;
                    let ln3 = this.allss[de].job.length;
                    let ln4 = this.allss[de].name.length;
                    let ln5 = this.allss[de].tel.length;
                    if(ln1 > 0 && ln2 > 0 && ln3 > 0 && ln4 > 0 && ln5 > 0){
                        if(ln1 == ln2 && ln3 == ln4 && ln4 == ln5){
                            $(_this).parent().parent().parent().css("border","1px solid lightgray")
                            contact.length = ln3;
                            gather.length = ln1;
                            this.allss[de].job.forEach((e,index) => {
                                contact[index] = {
                                    contact_name:this.allss[de].name[index],
                                    contact_job:this.allss[de].job[index],
                                    contact_cellphone:this.allss[de].tel[index]
                                }
                            });
                            this.allss[de].time.forEach((e,index) => {
                                gather[index] = {
                                    gather_date:this.allss[de].time[index],
                                    gather_address:this.allss[de].address[index]
                                }
                            });
                            every[idxss] = {
                                trip_date:de,
                                everyda_details:code,
                                contact:contact,
                                gather:gather
                            }
                        }else{
                            $(_this).parent().parent().parent().css("border","1px solid red")
                        }
                    }else{
                        $(_this).parent().parent().parent().css("border","1px solid red")
                    }
                    console.log(every);
                }

            },
            // 取消编辑
            canceledit(e) {
                var _this = e.currentTarget;
                $(_this).hide();
                $(_this).parent().children(".sonui_edit").text("编辑");
                $(_this).parent().parent().parent().attr("class","mui-table-view-cell mui-collapse");
                $(_this).parent().parent().parent().find(".add_trvel").hide();
                $(_this).parent().parent().parent().find(".time").children().remove();
                $(_this).parent().parent().parent().find(".address").children().remove();
                $(_this).parent().parent().parent().find(".job").children().remove();
                $(_this).parent().parent().parent().find(".ne").children().remove();
                $(_this).parent().parent().parent().find(".tel").children().remove();
                $(_this).parent().parent().parent().find(".tel").children().remove();
                $(_this).parent().parent().parent().find(".arrange").children("div").children().remove();
            },
            //行程安排
            arrange(e) {
                let _this = e.currentTarget;
                let flage_arrange = $(_this).parent().children(".add_trvel").css("display");
                flag = "arrange";
                indx = $(_this).parent().parent().parent().index();
                let prent = $(_this).parent().parent().parent();
                if (flage_arrange == "block") {
                    if(every[indx]){
                        var ssw = every[indx].everyda_details;
                        if(ssw){
                            var mark = base.decode(ssw);
                            $('#summernote').summernote('code',mark);
                            $(".form-control").attr("onchange", "getPhoto(this.files)")
                        }else{
                            $('#summernote').summernote('code','');
                            $(".form-control").attr("onchange", "getPhoto(this.files)")
                        }
                    }else{
                        $('#summernote').summernote('code','');
                        $(".form-control").attr("onchange", "getPhoto(this.files)")
                    }
                    let ht = $(_this).children("div").html();
                    if(ht){
                        $('#summernote').summernote('code',ht);
                        $(".form-control").attr("onchange", "getPhoto(this.files)")
                    }else{
                        $('#summernote').summernote('code','');
                        $(".form-control").attr("onchange", "getPhoto(this.files)")
                    }
                    $(".edit").show();
                    $("#masks").show();
                }
            },
            add(e){
                var _this = e.currentTarget;
                let pre = $(_this).parent().parent().parent().parent().parent();
                let item = pre.find(".times").text();
                let index = pre.index();
                let idx = $(_this).parent().index();
                if(this.item == ""){
                    this.item = item;
                }else if(item != this.item){
                    this.info1 = [];
                    this.info2 = [];
                    this.info3 = [];
                    this.info4 = [];
                    this.info5 = [];
                    this.item = item;
                }
                let txt = pre.find(".trvel_cont").eq(idx).children("input").val();
                if(txt){
                    if(idx == "0"){
                        this.info1.push(txt);
                        this.allss[item].time = this.info1;
                        pre.find(".time").append("<input type='text' value="+ txt +">");
                    }else if(idx == "1"){
                        this.info2.push(txt);
                        this.allss[item].address = this.info2;
                        pre.find(".address").append("<input type='text' value="+ txt +">");
                    }else if(idx == "2"){
                        this.info3.push(txt)
                        this.allss[item].job = this.info3;
                        pre.find(".job").append("<input type='text' value="+ txt +">");
                    }else if(idx == "3"){
                        this.info4.push(txt)
                        this.allss[item].name = this.info4;
                        pre.find(".ne").append("<input type='text' value="+ txt +">");
                    }else if(idx == "4"){
                        this.info5.push(txt)
                        this.allss[item].tel = this.info5;
                        pre.find(".tel").append("<input type='text' value="+ txt +">");
                    }
                    pre.find(".trvel_cont").eq(idx).children("input").val("")
                }else{
                    $(".tips span").text("不能为空哦~");
                    $("#masks,.tips").show();
                }
            },
            // 确定物品清单
            thing_sub(){
                inventory = []
                this.ss[0].forEach((e,index) => {
                    if(e && e.length > 0){
                        e.forEach(a => {
                            inventory.push({
                                inventory_class_name:this.thingList[index].class_name,
                                inventory_type_name:a
                            })
                        });
                    }
                });
                inventory = Array.from(new Set(inventory))
            },
            //最终提交按钮
            all_sub(){
                main.need = need;
                main.visa = visa;
                let ssg = 0;
                if(inventory.length <= 0){
                    // alert("物品清单不能为空~");
                    $(".tips span").text("物品清单不能为空~");
                    $("#masks,.tips").show();
                    return;
                }else{
                    main.inventory = inventory;
                }
                var file_trip_id = sessionStorage.getItem("file_trip_id");
                if(file_trip_id){
                    main.file_trip_id = file_trip_id;
                }else{
                    // alert("请选择封面图")
                    $(".tips span").text("请选择封面图~");
                    $("#masks,.tips").show();
                    return
                }
                for(var i = 0;i < every.length;i++){
                    if(!every[i]){
                        $(".right li").eq(i).css("border","1px solid red")
                    }else{
                        ssg++;
                    }
                }
                if(ssg == every.length){
                    main.everyday = every;
                }else{
                    $(".tips span").text("请完善行程信息~");
                    $("#masks,.tips").show();
                    // alert("请完善行程信息")
                    return
                }
                console.log(main);
                $.ajax({
                    type: "post",
                    dataType: "json",
                    url: this.baseurl + "/newTrip/saveNewTrip",
                    data: JSON.stringify(main),
                    headers: {
                        // token:getcookie('token')
                        token: "bO7Rj6PsPA2QbEhNuYt12f2C0eqQBnS+exmDK2ejcNmPJKCX9OEcIGHkuLk8QM9MB/A/KdXFGEyzFpGm8T1BeA=="
                    },
                    success: function (data) {
                        console.log(data)
                        // that.arrlist = data.data.sendData;
                        if(data.e.code == 0){
                            $(".tips span").text("团组信息保存成功~");
                            $("#masks,.tips").show();
                        }else{
                            $(".tips span").text(data.e.desc);
                            $("#masks,.tips").show();
                        }
                    },
                    error: function (err) {
                        console.log(err)
                    }
                })
                console.log(main)
            },
            // 获取集合时间
            getjigetime(e){
                let _this = e.currentTarget;
                tim = "gettime"
                $("#masks").show();
                $(".data").show();
                $("#enhms").click();
                $(".setok").text("确定");
                $(".today").text("现在");
                $(".clear").text("清除");
                $(".hmstitle p:eq(0)").text("时");
                $(".hmstitle p:eq(1)").text("分");
                $(".hmstitle p:eq(2)").text("秒");
                $(".timeheader").text("时间");
                $(".setok,.today").click(function(){
                    $("#masks").hide();
                    $(".data").hide();
                    let val = $("#enhms").val();
                    $(_this).val(val)
                })
                $(".clear").click(function(){
                    $("#masks").hide();
                    $(".data").hide();
                })
            },
            // 点击查看行程的列表
            aclk(e){
                let _this = e.currentTarget;
                let cla = $(_this).parent().parent().attr("class");
                $(".arrange").children("div").find("li").attr("class","");
                $(".time,.address,.job,.ne,.tel").find("input").css("border","none");
                if(cla == "mui-table-view-cell mui-collapse mui-active"){
                    $(_this).parent().parent().attr("class","mui-table-view-cell mui-collapse");    
                    $(_this).parent().parent().children(".mui-collapse-content").hide();
                }else{
                    $(_this).parent().parent().attr("class","mui-table-view-cell mui-collapse mui-active");
                    $(_this).parent().parent().children(".mui-collapse-content").show();
                }
            }
        },
        watch: {
            msg(val){
                console.log(val)
                if(val == ""){
                    $(".allname li").show()
                }else{
                    this.arrlist.forEach((e,index) => {
                        if(e.team_name.indexOf(val) != -1){
    
                        }else{
                            $(".allname li").eq(index).hide()
                        }
                    });
                }
              
            },
        }
    })
    $(".sub").on("click", function () {
        code = $('#summernote').summernote('code');
        let indexarr = [];
        let str = "";
        if(code.indexOf("data:image") != -1){
            var arr = code.split("<img");
            arr.forEach((e, index) => {
                if (e.indexOf("data:image") != -1) {
                    e = e.split(';">')[1];
                    arr[index] = e;
                    indexarr.push(index);
                }
            });
            imgsrc.forEach((e,index) => {
                let idx = indexarr[index];
                e = "<img src='"+ e + "'>"
                arr[idx] = e + arr[idx]
            })
            arr.forEach((e, index) => {
                str += e;
            });
            code = str;
        }
        console.log(code)
        code = base.encode(code);
        if(flag == "arrange"){
            // vm.arrange(event);
            var result2 = base.decode(code);
            $(".right .mui-table-view").children("li").eq(indx).find(".arrange").children("div").html("");
            $(".right .mui-table-view").children("li").eq(indx).find(".arrange").children("div").children().remove();
            $(".right .mui-table-view").children("li").eq(indx).find(".arrange").children("div").append(result2);
        }else if(flag == "create"){
            it.new_trip_need_content = code;
            let needs = {};
            needs.yoosure_address_id = it.yoosure_address_id;
            needs.address_class = it.address_class;
            needs.address_type = it.address_type;
            needs.new_trip_need_content = it.new_trip_need_content;
            need.push(needs);
            $(".needs").find(".no").children(".item").eq(indx).css("display","none");
            $(".needs").find("#needs").children().remove()
            need.forEach((e,index) => {
                $(".needs").find("#needs").append("<div class='item' idx='"+ index +"'><span>"+ e.address_type +"</span><button>编辑</button></div>")
            });
        }else if(flag == "visa"){
            its.new_trip_visa_content = code;
            let visas = {};
            visas.yoosure_address_id = its.yoosure_address_id;
            visas.address_class = its.address_class;
            visas.address_type = its.address_type;
            visas.new_trip_visa_content = its.new_trip_visa_content;
            visa.push(visas);
            $(".visa").find(".no").children(".item").eq(indx).css("display","none");
            $(".visa").find("#edit").children().remove()
            console.log(visa)
            visa.forEach((e,index) => {
                $(".visa").find("#edit").append("<div class='item' idx='"+ index +"'><span>"+ e.address_type +"</span><button>编辑</button></div>")
            });
        }else if(flag == "visa_btn"){
            visa[indx].new_trip_visa_content = code;
        }else if(flag == "create_btn"){
            need[indx].new_trip_need_content = code;
        }
        $(".edit").hide();
        $("#masks").hide();
        $('#summernote').summernote('destroy');
        imgsrc = [];
    })
    $("#needs").on("click",".item",function(){
        var cls = $(this).attr("idx");
        flag = "create_btn";
        indx = cls;
        var markupStr = need[indx].new_trip_need_content;
        markupStr = base.decode(markupStr);
        $('#summernote').summernote('code', markupStr);
        $(".form-control").attr("onchange", "getPhoto(this.files)")
        $(".edit").show();
        $("#masks").show();
    })

    $("#edit").on("click",".item",function(){
        var cls = $(this).attr("idx");
        flag = "visa_btn";
        indx = cls;
        var markupStr = visa[indx].new_trip_visa_content;
        console.log(markupStr)
        if(markupStr){
            markupStr = base.decode(markupStr);
            $('#summernote').summernote('code', markupStr);
            $(".form-control").attr("onchange", "getPhoto(this.files)")
            $(".edit").show();
            $("#masks").show();
        }else{
            $('#summernote').summernote('code', "");
            $(".form-control").attr("onchange", "getPhoto(this.files)")
            $(".edit").show();
            $("#masks").show();
        }
    })
    $(".choosename").focus(function(){
        $(".allname").show()
    });
    $(".container").on("click",function(){
        $(".allname").hide()
    })
    $(".tp").on("click",function(e){
        e.stopPropagation();
    })
    $("#masks").on("click",function(){
        if(tim == "gettime"){
            $("#masks").hide();
        }
    })

    $(".tips input").on("click",function(){
        $("#masks,.tips").hide();
    })
})
function getPhoto(files) {
    var filesSize = files[0].size / 1024 / 1024;
    var formdata = new FormData();
    formdata.append("file", files[0]);
    if (filesSize > 1) {
        alert("上传图片不能超过1M");
        return;
    } else {
        $.ajax({
            url: "http://101.200.83.32:8113/gold2/api/v1/uploadHandle/upload/trip_head",
            type: "POST",
            data: formdata,
            cache: false,
            processData: false,
            contentType: false,
            success: function (data) {
                var info = eval("(" + data + ")");
                var file_id = info.data.sendData.file.id;
                sessionStorage.setItem("file_trip_id", file_id)
                // let info 
                var imgurl = port + info.data.sendData.file.save_path + info.data.sendData.file.automatic_file_name;
                imgsrc.push(imgurl)
            }
        });
    }
}
