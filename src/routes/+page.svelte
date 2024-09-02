<script>
    import { Button, TextArea } from "carbon-components-svelte";
    import { Slider } from "carbon-components-svelte";
    let dnaSequence = "";
    let primerLength = 20;
    let gcContent = 50;

    async function submitForm() {
        const payload = {
            sequence: dnaSequence,
            primerLength: primerLength,
            gcContent: gcContent,
        };

        try {
            const response = await fetch("https://your-api-id.execute-api.region.amazonaws.com/prod/design-primers", {
                method: "POST",
                headers: {
                    "Content-Type": "application/json"
                },
                body: JSON.stringify(payload)
            });

            if (response.ok) {
                const result = await response.json();
                console.log("Designed Primers:", result);
                // Display the result in your UI
            } else {
                console.error("Failed to design primers:", response.statusText);
            }
        } catch (error) {
            console.error("Error:", error);
        }
    }
</script>

<form on:submit|preventDefault={submitForm}>
    <TextArea labelText="DNA sequence" bind:value={dnaSequence} placeholder="Enter a DNA sequence..." required/>
    <Slider labelText="Primer Length" bind:value={primerLength} min={10} max={30} required />
    <Slider labelText="GC Content (%)" bind:value={gcContent} min={40} max={60} required />
    <Button type="submit">Design Primers</Button>
</form>