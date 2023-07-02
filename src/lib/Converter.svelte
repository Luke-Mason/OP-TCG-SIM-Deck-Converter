<script lang="ts">
    let error = '';
    let loading = false
    let conversionStatus = 'content_paste_search'
    let convertedList = '';
    const countElements = (arr: any[]): { [key: string]: number } => {
        const counts: { [key: string]: number } = {};

        for (const element of arr) {
            if (counts[element]) {
                counts[element]++;
            } else {
                counts[element] = 1;
            }
        }

        return counts;
    }

    const executeTTSStrategy = (rawDeckList: string[]) => {
        const counts = countElements(rawDeckList.slice(1));
        const list = Object.entries(counts).map(([key, value]) => {
            return `${value}x${key}`
        })
        return list.join('\n');
    }

    const executeOPTDStrategy = (rawDeckListStr: string) => {

        // Cut off all other text before first '(' char except 2 chars before.
        const sliceIndexStart = rawDeckListStr.indexOf('(') - 2;
        const sliceIndexLast = rawDeckListStr.lastIndexOf(')') + 1;
        rawDeckListStr = rawDeckListStr.slice(sliceIndexStart, sliceIndexLast);

        // Regex replace ( or ) with blan
        const regex = /(\d+)\s\((\w+-\d+)\)/g;
        return rawDeckListStr.replace(regex, '$1x$2').split(' ').join('\n');
    }

    const convert = (rawDeckListStr) => {

        // Ignore output as input
        if (convertedList.replaceAll('\n', '').replaceAll(' ', '') === rawDeckListStr.replaceAll('\n', '').replaceAll(' ', '')) {
            loading = false;
            return;
        }

        console.log('Converting...', rawDeckListStr);
        let list = '';
        error = '';

        // Try to convert to array of string from JSON
        try {
            const rawDeckList = JSON.parse(rawDeckListStr)
            // If it succeeds then it is in TTS format
            list = executeTTSStrategy(rawDeckList);
        } catch (e) {
            // If it fails then it is in onepiecetopdecks format
            list = executeOPTDStrategy(rawDeckListStr);
        }


        // Verify
        if (list.split('\n').length < 2) {
            error = `Conversion did not look correct, this was the input: '${rawDeckListStr}', and this is the output: '${list}'`;
            throw new Error(error);
        }

        convertedList = list;
    }

    const copyListToClipboard = async () => {
        try {
            await navigator.clipboard.writeText(convertedList);
            console.log('Text copied to clipboard:', convertedList);
        } catch (e) {
            if (e.toString() === 'NotAllowedError: Document is not focused.') {
                try {
                    await navigator.clipboard.writeText(convertedList);
                    console.log('Text copied to clipboard:', convertedList);
                } catch (e) {
                    console.error('Failed to copy text:', e);
                    error = 'Failed to copy text:' + e;
                }
                return;
            } else {
                console.error('Failed to copy text:', e);
                error = 'Failed to copy text:' + e;
            }
        }
    }

    async function handleCopy() {
        conversionStatus = 'content_paste_search';

        try {
            loading = true;
            const clipboardData = await navigator.clipboard.readText();
            console.log('Clipboard data:', clipboardData);

            convert(clipboardData);

            await copyListToClipboard();
        } catch (e) {
            console.error('Failed to read clipboard contents:', e);
            error = 'Failed to read clipboard contents: ' + e;
        }
        loading = false;

        if (!loading && !error && convertedList) {
            conversionStatus = 'done';
        } else if (!loading && error) {
            conversionStatus = 'error';
        } else if (loading && !error) {
            conversionStatus = 'loading';
        }
    }

    import Tab, {Icon, Label} from '@smui/tab';
    import TabBar from '@smui/tab-bar';

    type TabEntry = {
        icon: string;
        label: string;
    };
    let tabs: TabEntry[] = [
        {
            icon: 'auto_fix_high',
            label: 'Auto',
        },
        {
            icon: 'edit',
            label: 'Manual',
        },
    ];
    let active = tabs[0];

    import CircularProgress from '@smui/circular-progress';

</script>


<div class="converter-container">
    <div class="error">{error}</div>
    <TabBar {tabs} let:tab bind:active>
        <Tab {tab} class="tab">
            <Icon class="material-icons">{tab.icon}</Icon>
            <Label>{tab.label}</Label>
        </Tab>
    </TabBar>
    <div class="content-wrapper">
        {#if active.label === 'Auto'}
            <div class="auto-content-wrapper">
                <button class="auto-button" on:click={handleCopy}>Convert from and into clipboard</button>
                {#if !loading}
                    <Icon class="material-icons">{conversionStatus}</Icon>
                {:else}
                    <div style="display: flex; justify-content: center">
                        Loading...
                    </div>
                {/if}
                {#if convertedList.length > 0}
                    <div>
                        <h5>Output</h5>
                        <div class="result">{convertedList}</div>
                    </div>
                {/if}
            </div>
        {:else if active.label === 'Manual'}
            <div class="manual-content-wrapper">
                <textarea class="textarea" on:input={(e) => convert(e.target.value)}></textarea>
                <div>
                    to
                </div>
                {#if convertedList.length > 0}
                    <div>
                        <h5>Output</h5>
                        <div class="result">{convertedList}</div>
                    </div>
                    <button on:click={async () => await copyListToClipboard()}>Copy to Clipboard</button>
                {/if}
            </div>
        {/if}
    </div>
</div>


<style>
    .error {
        color: red;
        font-weight: bold;
        overflow-wrap: anywhere;
    }
    .manual-content-wrapper {
        display: flex;
        flex-direction: row;
        align-items: center;
        gap: 1rem;
        height: 90%;
    }

    .auto-content-wrapper {
        display: flex;
        flex-direction: row;
        margin: auto;
        width: 100%;
        justify-content: center;
        gap: 1rem;
        align-items: baseline;
    }

    .converter-container {
        display: flex;
        flex: 1;
        flex-direction: column;
    }

    .content-wrapper {
        display: flex;
        flex: 1;
        justify-content: center;
        align-items: center;
    }

    .result {
        white-space: pre-line;
    }

    .textarea {
        display: flex;
        height: 100%;
        min-height: 10rem;
    }
</style>
<!--From this



-->
<!-- Become this

1xOP01-062
2xOP01-065
2xOP01-067
2xOP01-070
4xOP01-074
3xOP01-075
4xOP01-078
4xOP01-079
4xST03-007
3xST03-012
2xOP01-097
2xOP01-110
4xST04-002
2xST04-003
4xOP01-086
4xOP01-088
4xST03-017

-->