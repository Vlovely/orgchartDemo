 // 模拟请求数据
    $.mockjax({
      url: '/orgchart/children',
      contentType: 'application/json',
      responseTime: 1000,
      responseText: { 'children': [
        { 'id': 'id6', 'title': '二2-三1', 'type': '2' },
        { 'id': 'id7', 'title': '二2-三2', 'type': '2' }
      ]}
    });
    // 默认数据
    var datascource = {
      'id': 'id1',
      'title': '一',
      'type': '1',
      'relationship': '001', 
      //第一个字符代表当前节点是否具有父节点；
      //第二个字符代表当前节点有同级节点；
      //第三个字符代表当前节点是否有子节点。
      'children': [
        {'id': 'id2', 'title': '二1', 'type': '2' , 'children': [
          {
            'id': 'id3',
            'title': '二1-三1',
            'type': '3'
          }
        ]},
        {'id': 'id4', 'title': '二2', 'type': '2' },
        {'id': 'id5', 'title': '二3', 'type': '2' },
      ]
    };
    // HTML模板
    var nodeTemplate = function(data) {
      let iconName
      if (data.children && data.children.length > 0) {
        iconName = 'icon-suoxiao'
      } else {
        iconName = 'icon-fangda'
      }
      return `
      <img src="./img/avatar/${data.type}.jpg" style="width:100%" title="${data.title}"/>
      <span>${data.title}</span>
      <i class="iconfont ${iconName}" style="width:14px;height:14px;top:-4px;left:-4px;position:absolute;"></i>
      `;
    };
    // 初始化
    const oc = $('#chart-container').orgchart({
      'data' : datascource,
      'visibleLevel': '3', // 它表示在最开始的orgchart扩展到的级别。
      'nodeTemplate': nodeTemplate,
      'nodeId': 'id'
    });
    // 绑定点击事件
    $('#chart-container').delegate('.icon-suoxiao', 'click', function() {
      $(this).removeClass('icon-suoxiao').addClass('icon-fangda');
      $(this).parent().parent().parent().siblings().hide()
    })
    $('#chart-container').delegate('.icon-fangda', 'click', function() {
      if ($(this).parent().parent().parent().siblings().length > 0) {
        $(this).parent().parent().parent().siblings().show()
        $(this).removeClass('icon-fangda').addClass('icon-suoxiao');
      } else if ($(this).parent().attr('id') === 'id4') {
        let that = this
        $.post("/orgchart/children", function(res) {
          oc.addChildren($(that).parent(), res.children)
          $(that).removeClass('icon-fangda').addClass('icon-suoxiao');
        });
      }
    })
