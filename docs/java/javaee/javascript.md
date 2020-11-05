### 1. 静态资源引入问题

```javascript
<script src="/lib/editormd/editormd.min.js" th:src="@{/lib/editormd/editormd.min.js}"></script>
```

```javascript
src="../../static/lib/editormd/editormd.min.js" 在静态环境中的磁盘路径下寻找（非web环境下的测试测用）。
src="/lib/editormd/editormd.min.js"                会在当前路径下的lib里寻找th:src="@{/lib/editormd/editormd.min.js}"          使用模板后会在项目路径下的static下寻找
```

## 2.

