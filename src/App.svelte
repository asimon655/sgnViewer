<script>
    let files;
    let xmlDoc;
    let pdfUrl;
    let error;
    let errorResetter;

    $: if (error) {
        if (errorResetter){
            clearTimeout(errorResetter)
        }
        errorResetter = setTimeout(()=> {error = undefined; errorResetter=undefined},4000)
    }

    document.body.ondragover = function(evt) {
        evt.preventDefault()
    };
    document.body.ondrop = function(evt) {
        evt.preventDefault()
        files = evt.dataTransfer.files;
    }

    $: if (files && files[0]) readXML(files[0])

    $: if (xmlDoc) savePdf(xmlDoc.querySelector("SignaturePackage")
                                    .querySelector("Signature")
                                    .querySelector("Object")
                                    .querySelector("DocumentContent")
                                    .textContent)

    function readXML(file) {
        var xhr = new XMLHttpRequest();
        xhr.open('GET', URL.createObjectURL(file), true)

        xhr.timeout = 2000; // time in milliseconds

        xhr.onload = function () {
        // Request finished. Do processing here.
            try {
                xmlDoc = this.responseXML // <- Here's your XML file
                if (!xmlDoc) error = "not a good file"
            } catch {
                error = "not a good file"
            }
        };

        xhr.onerror = function () {
            error = "error"
        }

        xhr.send(null);
    }

    function savePdf(text_b64) {
        pdfUrl = "data:application/pdf;base64," + text_b64;
    }
</script>

<svelte:head>
    <title>open sng files</title>
</svelte:head>

<div class="bottom">
    made by asimon655 NaNach ;)
</div>
<div class="top">
    בס״ד
</div>

<input type="file" bind:files>

{#if xmlDoc}
    <p>got ya!</p>
{:else if error}
    <p class="error">{error}</p>
{:else}
    <p>or drop a file anywhere</p>
{/if}

{#if pdfUrl}
    <iframe src={pdfUrl} title="pdf content">
        <p>this is not supported in your browser</p>
    </iframe>
{/if}


<style>
    iframe {
        border: 0;
        width: 90vw;
        height: 90vh;
    }
    .error {
        color: red;
    }
    p {
        font-weight: bold;
    }
    input {
        font-weight: bold;
    }
    .bottom {
        position: fixed;
        bottom: 0;
        left: 0;
        font-size: smaller;
        color: rgb(250, 162, 213);
        opacity: 50%;
        font-family: 'Times New Roman', Times, serif;
        z-index: -1;
    }
    .top {
        position: absolute;
        top: 0;
        right: 0;
        font-size: smaller;
        color: rgb(250, 162, 213);
        opacity: 50%;
    }
</style>