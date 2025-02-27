<script lang="ts">
	import { Badge } from '$lib/components/common'
	import type { FlowModule } from '$lib/gen'
	import { classNames } from '$lib/utils'
	import { createEventDispatcher, getContext } from 'svelte'
	import type { FlowCopilotContext } from '$lib/components/copilot/flow'

	export let label: string
	export let bgColor: string = ''
	export let selected: boolean
	export let selectable: boolean
	export let id: string | undefined = undefined
	export let center = true
	export let borderColor: string | undefined = undefined
	export let hideId: boolean = false

	const dispatch = createEventDispatcher<{
		insert: {
			script?: { path: string; summary: string; hash: string | undefined }
			detail: 'script' | 'forloop' | 'branchone' | 'branchall' | 'trigger' | 'move'
			modules: FlowModule[]
			index: number
		}
		select: string
	}>()

	const { currentStepStore: copilotCurrentStepStore } =
		getContext<FlowCopilotContext | undefined>('FlowCopilotContext') || {}
</script>

<!-- svelte-ignore a11y-click-events-have-key-events -->
<!-- svelte-ignore a11y-no-static-element-interactions -->
<div
	class={classNames(
		'w-full flex relative overflow-hidden rounded-sm',
		selectable ? 'cursor-pointer' : '',
		selected ? 'outline outline-offset-1 outline-2  outline-gray-600' : '',
		label === 'Input' && $copilotCurrentStepStore === 'Input' ? 'z-[901]' : '',
		'bg-surface'
	)}
	style="width: 275px; max-height: 34px; background-color: {bgColor} !important;"
	on:click={() => {
		if (selectable) {
			if (id) {
				dispatch('select', id)
			} else {
				dispatch('select', label)
			}
		}
	}}
	id={`flow-editor-virtual-${label}`}
>
	<div
		style={borderColor ? `border-color: ${borderColor};` : ''}
		class="flex gap-1 justify-between {center
			? 'items-center'
			: 'items-baseline'} w-full overflow-hidden rounded-sm border p-2 text-2xs module text-primary border-gray-400 dark:border-gray-600"
	>
		{#if $$slots.icon}
			<slot name="icon" />
			<span class="mr-2" />
		{/if}
		<div />
		<div class="truncate"><pre>{label}</pre></div>
		<div class="flex items-center space-x-2">
			{#if id && !hideId}
				<Badge color="indigo">{id}</Badge>
			{/if}
		</div>
	</div>
</div>
