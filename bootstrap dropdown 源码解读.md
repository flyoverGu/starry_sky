# bootstrap dropdown 源码解读

### 前言  
    bootstrap 是一套成熟的css框架，被许多项目所采用。例如我们的[项目](http://dida365.com)。  
    既然用到了，就好好了解一下，涨涨知识。我们主要是用了其中dropdown库。
    看了一下它的js源码。我觉得还是有许多借鉴的地方的。
    特写小文一片以做记录。

## dropdown干嘛用的？  
    dropdown就是个下拉菜单的组件。主要控制着菜单出现和消失。  
    
````
<div class="dropdown">
    <a data-toggle="dropdown"> menu </a>
        <ul class="dropdown-menu">
            <li> <a> option1 </a> </li>
            <li> <a> option2 </a> </li>
            <li> <a> option3 </a> </li>
            <li> <a> option4 </a> </li>
        </ul>
</div>
````
看一下效果: [demo](http://jsfiddle.net/flyover/L943rwfb/);

## 看源码  
    鄙视我吧，我就是直接贴源码了。 在关键地方写了注释。

````
/* ========================================================================
 * Bootstrap: dropdown.js v3.3.5
 * http://getbootstrap.com/javascript/#dropdowns
 * ========================================================================
 * Copyright 2011-2015 Twitter, Inc.
 * Licensed under MIT (https://github.com/twbs/bootstrap/blob/master/LICENSE)
 * ======================================================================== */


+function ($) {
  'use strict';

  // DROPDOWN CLASS DEFINITION
  // =========================

  var backdrop = '.dropdown-backdrop'
  var toggle   = '[data-toggle="dropdown"]'
  // 写个构造函数 这样就可以采用 new Dropdown(option); 的方式初始化
  // 这是使用js的方式初始化
  var Dropdown = function (element) { 
    // 全局监听click事件
    $(element).on('click.bs.dropdown', this.toggle)
  }

  Dropdown.VERSION = '3.3.5'

  // 找到目标节点
  // 三种方式
  // 1. 通过data-target
  // 2. href中hash
  // 3. $this 的直属父节点
  function getParent($this) {
    var selector = $this.attr('data-target')

    if (!selector) {
      selector = $this.attr('href')
      selector = selector && /#[A-Za-z]/.test(selector) && selector.replace(/.*(?=#[^\s]*$)/, '') // strip for ie7
    }

    var $parent = selector && $(selector)

    return $parent && $parent.length ? $parent : $this.parent()
  }

  // 关闭菜单
  function clearMenus(e) {
    if (e && e.which === 3) return
    $(backdrop).remove()
    // 这里会关闭所有菜单
    $(toggle).each(function () {
      var $this         = $(this)
      var $parent       = getParent($this)
      var relatedTarget = { relatedTarget: this }

      if (!$parent.hasClass('open')) return

      // 如果点击菜单中的 输入框 则不隐藏菜单
      if (e && e.type == 'click' && /input|textarea/i.test(e.target.tagName) && $.contains($parent[0], e.target)) return


      // 触发事件
      $parent.trigger(e = $.Event('hide.bs.dropdown', relatedTarget))

      if (e.isDefaultPrevented()) return

      $this.attr('aria-expanded', 'false')
      // 触发事件
      $parent.removeClass('open').trigger('hidden.bs.dropdown', relatedTarget)
    })
  }

  Dropdown.prototype.toggle = function (e) {
    var $this = $(this)

    // 判断 li 是否被禁止点击
    if ($this.is('.disabled, :disabled')) return

    // (找爹) 获取需要被打开的控件
    var $parent  = getParent($this)
    var isActive = $parent.hasClass('open')

    // 首先关闭所有菜单
    // 这里会排他，导致其他菜单被关闭
    clearMenus()

    // 如果原先没有被打开，就打开菜单
    if (!isActive) {

      // 兼容移动端点击其他区域 让菜单消失
      if ('ontouchstart' in document.documentElement && !$parent.closest('.navbar-nav').length) {
        // if mobile we use a backdrop because click events don't delegate
        $(document.createElement('div'))
          .addClass('dropdown-backdrop')
          .insertAfter($(this))
          .on('click', clearMenus)
      }

      var relatedTarget = { relatedTarget: this }
      // 触发 show.bs.dropdown 事件
      $parent.trigger(e = $.Event('show.bs.dropdown', relatedTarget))

      if (e.isDefaultPrevented()) return

      $this
        .trigger('focus')
        .attr('aria-expanded', 'true')

      // 触发 shown.bs.dropdown 事件
      // 如果要在某个菜单打开之后 做某些事 可以监听这个事件
      $parent
        .toggleClass('open')
        .trigger('shown.bs.dropdown', relatedTarget)
    }

    return false
  }

  Dropdown.prototype.keydown = function (e) {
    if (!/(38|40|27|32)/.test(e.which) || /input|textarea/i.test(e.target.tagName)) return

    var $this = $(this)

    e.preventDefault()
    e.stopPropagation()

    if ($this.is('.disabled, :disabled')) return

    var $parent  = getParent($this)
    var isActive = $parent.hasClass('open')

    if (!isActive && e.which != 27 || isActive && e.which == 27) {
      if (e.which == 27) $parent.find(toggle).trigger('focus')
      return $this.trigger('click')
    }

    var desc = ' li:not(.disabled):visible a'
    var $items = $parent.find('.dropdown-menu' + desc)

    if (!$items.length) return

    var index = $items.index(e.target)

    if (e.which == 38 && index > 0)                 index--         // up
    if (e.which == 40 && index < $items.length - 1) index++         // down
    if (!~index)                                    index = 0

    $items.eq(index).trigger('focus')
  }


  // DROPDOWN PLUGIN DEFINITION
  // ==========================

  function Plugin(option) {
    return this.each(function () {
      var $this = $(this)
      var data  = $this.data('bs.dropdown')

      if (!data) $this.data('bs.dropdown', (data = new Dropdown(this)))
      if (typeof option == 'string') data[option].call($this)
    })
  }

  var old = $.fn.dropdown

  $.fn.dropdown             = Plugin
  $.fn.dropdown.Constructor = Dropdown


  // DROPDOWN NO CONFLICT
  // ====================

  $.fn.dropdown.noConflict = function () {
    $.fn.dropdown = old
    return this
  }


  // APPLY TO STANDARD DROPDOWN ELEMENTS
  // ===================================

  $(document)
    .on('click.bs.dropdown.data-api', clearMenus)
    .on('click.bs.dropdown.data-api', '.dropdown form', function (e) { e.stopPropagation() })
    .on('click.bs.dropdown.data-api', toggle, Dropdown.prototype.toggle)
    .on('keydown.bs.dropdown.data-api', toggle, Dropdown.prototype.keydown)
    .on('keydown.bs.dropdown.data-api', '.dropdown-menu', Dropdown.prototype.keydown)

}(jQuery);

````

