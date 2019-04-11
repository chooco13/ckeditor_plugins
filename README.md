# ckeditor plugin tip share

this is just how I did.

you can make more convenient if you have better idea
I just want to share my experience.

It is based on `CKEditor 4.11.3`

## image uploader (ezimage)
It will change more user-friendly.
not using dialog that make hard to use.

`/{your resource path}/ckeditor/plugins/ezimage/plugin.js`
```
CKEDITOR.plugins.add('ezimage', {
    icons: 'ezimageIcon',
    init: function (editor) {
        editor.addCommand('insertEzImage', {
            exec: function (editor) {
                $('#ezImageInput').click();
            }
        });

        editor.ui.addButton('EzImage', {
            label: 'Insert Image',
            command: 'insertEzImage'
        });
    }
});
```

```
<div class="form-group">
    <label for="content">content</label>
    <form:textarea path="content" name="editor" id="editor" rows="20" cols="80"/>
    <input type="file" class="d-none" id="ezImageInput"/>
</div>

<script>
    $(function () {
        var editor = CKEDITOR.replace( 'editor', {
            extraPlugins: 'ezimage',
            toolbar: [
                // add what you needs
                { name: 'insert', items: [ 'EzImage'] }
            ],
            allowedContent: true
        });

        $('#ezImageInput').on('change', function () {
            console.log('change');

            var formData = new FormData();
            formData.append("upload", document.querySelector('#ezImageInput').files[0]);

            axios.post('{your image upload api url}', formData, {headers: {'Content-Type': 'multipart/form-data'}})
                .then(function (response) {
                    console.log(response.data);
                    
                    // handle your upload api result.

                    $('#ezImageInput').val(null);
                })
                .catch(function (error) {
                    console.log(error);
                });
        });
    })
</script>
```
![ezimage_example1](https://github.com/chooco13/ckeditor_plugins/raw/master/resources/ezimage%20example.png)  
![ezimage_example2](https://github.com/chooco13/ckeditor_plugins/raw/master/resources/ezimage%20example2.png)

## iframe insert
just modify `iframe.js` in original plugin

`/{your resource path}/ckeditor/plugins/iframe/dialogs/plugin.js`
```
(function () {
    function c(b) {
        var c = this instanceof CKEDITOR.ui.dialog.checkbox;
        b.hasAttribute(this.id) && (b = b.getAttribute(this.id), c ? this.setValue(e[this.id]["true"] == b.toLowerCase()) : this.setValue(b))
    }

    function d(b) {
        var c = "" === this.getValue(), a = this instanceof CKEDITOR.ui.dialog.checkbox, d = this.getValue();
        c ? b.removeAttribute(this.att || this.id) : a ? b.setAttribute(this.id, e[this.id][d]) : b.setAttribute(this.att || this.id, d)
    }

    var e = {scrolling: {"true": "yes", "false": "no"}, frameborder: {"true": "1", "false": "0"}};
    CKEDITOR.dialog.add("iframe", function (b) {
        var f = b.lang.iframe, a = b.lang.common;
        return {
            title: f.title, minWidth: 350, minHeight: 100, onShow: function () {
                this.fakeImage = this.iframeNode = null;
                var a = this.getSelectedElement();
                a && a.data("cke-real-element-type") && "iframe" == a.data("cke-real-element-type") && (this.fakeImage = a, this.iframeNode = a = b.restoreRealElement(a), this.setupContent(a))
            }, onOk: function (e) {
                var a;
                a = new CKEDITOR.dom.element("iframe");
                this.commitContent(a);

                if (a.$.attributes[0].nodeValue.match(/^<iframe(.+)<\/iframe>/)) {
                    b.insertHtml(a.$.attributes[0].nodeValue);
                } else {
                    alert('not valid iframe tag format.');
                }
            }, contents: [{
                id: "info",
                label: a.generalTab,
                accessKey: "I",
                elements: [{
                    type: "vbox",
                    padding: 0,
                    children: [{
                        id: "src",
                        type: "text",
                        label: 'source code',
                        required: !0,
                        validate: CKEDITOR.dialog.validate.notEmpty(f.noUrl),
                        setup: c,
                        commit: d
                    }]
                }]
            }]
        }
    })
})();

```
```
<script>
    $(function () {
        var editor = CKEDITOR.replace( 'editor', {
            toolbar: [
                // add what you needs
                { name: 'insert', items: [ 'Iframe'] }
            ],
            allowedContent: true
        });
    });
</script>
```
![iframe_example](https://github.com/chooco13/ckeditor_plugins/raw/master/resources/iframe%20example.png)
