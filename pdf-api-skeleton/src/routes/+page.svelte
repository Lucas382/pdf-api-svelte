<script lang="ts">
    import {
        FileButton,
        FileDropzone,
        ListBox,
        ListBoxItem,
    } from "@skeletonlabs/skeleton";

    let files: FileList;
    let visible: boolean = true;
    let apiURL: string = "https://pdftojsonapi-production.up.railway.app";
    let valueSingle: string = "books";

    $: reportLink = "";

    $: buttonText = "View Selected Report";

    function getPDFById(e: Event): void {
        const selectedValue = (e.target as HTMLSelectElement).value;

        const btnSelected = document.getElementById(
            "btn-selected-pdf",
        ) as HTMLAnchorElement;
        reportLink = `${apiURL}/pdf_by_id/${selectedValue}`;
        console.log(reportLink);
    }

    function openPDF() {
        const newWindow = window.open(reportLink, "_blank");

        if (!newWindow) {
            console.error("The window opening was blocked by the browser.");
        }
    }

    async function sendPDFFile(file: File): Promise<void> {
        const url = `${apiURL}/extract_object`;

        const formData = new FormData();
        formData.append("pdf_file", file, "1.pdf");

        try {
            const response = await fetch(url, {
                method: "POST",
                body: formData,
            });

            if (response.ok) {
                // Aqui você pode lidar com a resposta da solicitação bem-sucedida
                const jsonResponse = await response.json();
                console.log("Server Response:", jsonResponse);

                const formattedJSON = JSON.stringify(jsonResponse, null, 2);

                const newWindow = window.open();

                if (newWindow) {
                    newWindow.document.write(`<pre>${formattedJSON}</pre>`);
                } else {
                    console.error(
                        "The window oppening was blocked by the browser.",
                    );
                }
            } else {
                console.error("Erro to send file:", response.statusText);
            }
        } catch (error) {
            console.error("Erro on solicitation:", error);
        }
    }

    function pdfLoaded(event: Event): void {
        const statusMessage = document.getElementById("status-message");

        if (statusMessage) {
            if (event instanceof DragEvent && event.dataTransfer) {
                // Se um arquivo for arrastado e solto
                const file = event.dataTransfer.files[0];
                statusMessage.textContent = `File ${file.name} loaded.`;
            } else if (
                event instanceof Event &&
                event.target instanceof HTMLInputElement
            ) {
                // Se um arquivo for selecionado usando o botão "Upload a file"
                const fileInput = event.target;
                if (fileInput.files && fileInput.files.length > 0) {
                    const file = fileInput.files[0];
                    statusMessage.textContent = `File ${file.name} loaded.`;
                }
            }
        }
    }
</script>

<!-- YOU CAN DELETE EVERYTHING IN THIS PAGE -->

<div class="container h-full mx-auto flex justify-center items-center">
    <div class="space-y-3 space-x-3">
        <hr />
        <p style="font-size: 25px; color:gray;">Download a example Report:</p>

        <ListBox>
            <ListBoxItem
                bind:group={valueSingle}
                on:click={getPDFById}
                name="medium"
                value="1">Reports example (1)</ListBoxItem
            >
            <ListBoxItem
                bind:group={valueSingle}
                on:click={getPDFById}
                name="medium"
                value="2">Reports example (2)</ListBoxItem
            >
            <ListBoxItem
                bind:group={valueSingle}
                on:click={getPDFById}
                name="medium"
                value="3">Reports example (3)</ListBoxItem
            >
        </ListBox>

        <button
            on:click={() => {
                if (reportLink != "") {
                    openPDF();
                }
            }}
            id="btn-selected-pdf"
            class="btn variant-filled-primary"
        >
            {buttonText}
        </button>
        <hr />

        <FileDropzone
            name="file"
            on:drop={(e) => pdfLoaded(e)}
            on:change={(e) => pdfLoaded(e)}
            bind:files
        >
            <svelte:fragment slot="message">
                <p id="status-message">Upload a file or drag and drop</p>
            </svelte:fragment>
            <svelte:fragment slot="meta">.PDF</svelte:fragment>
        </FileDropzone>
        <button
            class="btn variant-filled-secondary"
            on:click={() => {
                if (files) {
                    sendPDFFile(files[0]);
                } else {
                    console.log("No file loaded.");
                }
            }}>Submit</button
        >
        <hr />
    </div>
</div>
