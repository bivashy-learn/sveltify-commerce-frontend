<script lang="ts">
    import { Button } from "$lib/components/ui/button";
    import * as Collapsible from "$lib/components/ui/collapsible";
    import { Input } from "$lib/components/ui/input";
    import { Search, ChevronsUpDown } from "lucide-svelte";

    let categories = [
        { name: "Accessories", items: ["Bags", "Belts", "Scarves", "Hats"] },
        { name: "Clothing", items: ["Shirts", "Pants", "Jackets", "Shoes"] },
    ];

    let searchTerm = "";

    function getHighlightedParts(text: string, query: string) {
        if (!query) return [{ text, isMatch: false }];

        const regex = new RegExp(`(${query})`, "gi");
        const parts = [];
        let lastIndex = 0;

        text.replace(regex, (match: string, ignored: string, offset: int) => {
            if (offset > lastIndex) {
                parts.push({
                    text: text.slice(lastIndex, offset),
                    isMatch: false,
                });
            }
            parts.push({ text: match, isMatch: true });
            lastIndex = offset + match.length;
        });

        if (lastIndex < text.length) {
            parts.push({ text: text.slice(lastIndex), isMatch: false });
        }

        return parts;
    }

    function getFilteredItems(items: string[], query: string) {
        return items.filter((item) =>
            item.toLowerCase().includes(query.toLowerCase()),
        );
    }
</script>

<div class="relative">
    <Search
        class="text-muted-foreground absolute left-2 top-[50%] h-4 w-4 translate-y-[-50%]"
    />
    <Input placeholder="Search" class="pl-8 bg-acc" bind:value={searchTerm} />
</div>

{#each categories as category}
    <Collapsible.Root
        class="space-y-2"
        open={searchTerm
            ? !!getFilteredItems(category.items, searchTerm).length
            : false}
    >
        <div class="flex items-center justify-between">
            <Collapsible.Trigger asChild let:builder>
                <Button builders={[builder]} class="space-x-2" variant="ghost">
                    <h4 class="text-base font-bold">{category.name}</h4>
                    <ChevronsUpDown size={16}></ChevronsUpDown>
                    <span class="sr-only">Toggle</span>
                </Button>
            </Collapsible.Trigger>
        </div>
        <Collapsible.Content class="space-y-1">
            {#each getFilteredItems(category.items, searchTerm) as item}
                <div
                    class="rounded-md px-4 font-bold text-muted-foreground hover:underline"
                >
                    {#each getHighlightedParts(item, searchTerm) as part}
                        <span class={part.isMatch ? "bg-yellow-300" : ""}
                            >{part.text}</span
                        >
                    {/each}
                </div>
            {/each}
        </Collapsible.Content>
    </Collapsible.Root>
{/each}
