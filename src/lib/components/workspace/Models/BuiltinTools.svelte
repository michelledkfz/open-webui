<script lang="ts">
	import { getContext } from 'svelte';
	import Tooltip from '$lib/components/common/Tooltip.svelte';
	import { marked } from 'marked';

	const i18n = getContext('i18n');

	const toolLabels = {
		time: {
			label: $i18n.t('Time & Calculation'),
			description: $i18n.t('Get current time and perform date/time calculations')
		},
		memory: {
			label: $i18n.t('Memory'),
			description: $i18n.t('Search and manage user memories')
		},
		chats: {
			label: $i18n.t('Chat History'),
			description: $i18n.t('Search and view user chat history')
		},
		notes: {
			label: $i18n.t('Notes'),
			description: $i18n.t('Search, view, and manage user notes')
		},
		knowledge: {
			label: $i18n.t('Knowledge Base'),
			description: $i18n.t('Browse and query knowledge bases')
		},
		files: {
			label: $i18n.t('Files'),
			description: $i18n.t('List, search, and read files attached to the current chat')
		},
		channels: {
			label: $i18n.t('Channels'),
			description: $i18n.t('Search channels and channel messages')
		},
		notifications: {
			label: $i18n.t('Notifications'),
			description: $i18n.t('Send notifications to configured webhook targets')
		},
		web_search: {
			label: $i18n.t('Web Search'),
			description: $i18n.t('Search the web and fetch URLs')
		},
		image_generation: {
			label: $i18n.t('Image Generation'),
			description: $i18n.t('Generate and edit images')
		},
		code_interpreter: {
			label: $i18n.t('Code Interpreter'),
			description: $i18n.t('Execute code')
		},
		tasks: {
			label: $i18n.t('Task Management'),
			description: $i18n.t('Break down complex requests into trackable steps')
		},
		automations: {
			label: $i18n.t('Automations'),
			description: $i18n.t('Create and manage scheduled automations')
		},
		calendar: {
			label: $i18n.t('Calendar'),
			description: $i18n.t('List calendars, search, create, update, and delete calendar events')
		},
		subagents: {
			label: $i18n.t('Sub-agents'),
			description: $i18n.t('Delegate focused work to parallel sub-agents')
		}
	};

	const allTools = Object.keys(toolLabels) as Array<keyof typeof toolLabels>;

	// Each category is a tri-state:
	//   - 'auto'   (absent / true)  -> the model decides for itself (default)
	//   - 'manual' ('manual')       -> only used when the user enables it in the chat
	//   - 'off'    (false)          -> never injected
	export let builtinTools: Record<string, boolean | 'manual'> = {};

	const modeOptions = ['auto', 'manual', 'off'] as const;
	type Mode = (typeof modeOptions)[number];

	function currentMode(tool: string): Mode {
		if (builtinTools[tool] === false) return 'off';
		if (builtinTools[tool] === 'manual') return 'manual';
		return 'auto';
	}

	function setMode(tool: string, mode: Mode) {
		if (mode === 'off') {
			builtinTools[tool] = false;
		} else if (mode === 'manual') {
			builtinTools[tool] = 'manual';
		} else {
			delete builtinTools[tool];
		}
		builtinTools = builtinTools;
	}
</script>

<div>
	<div class="mb-1.5 text-xs text-gray-400 dark:text-gray-600">{$i18n.t('Builtin Tools')}</div>
	<div class="grid grid-cols-1 gap-x-5 gap-y-1 sm:grid-cols-2 lg:grid-cols-3">
		{#each allTools as tool}
			<div class="flex min-h-6 items-center gap-2.5">
				<div class="min-w-0 flex-1 text-xs text-gray-600 dark:text-gray-400">
					<Tooltip content={marked.parse(toolLabels[tool].description)}>
						<span class="truncate">{$i18n.t(toolLabels[tool].label)}</span>
					</Tooltip>
				</div>
				<select
					class="h-7 w-28 rounded border border-gray-300 bg-transparent px-1 text-xs text-gray-600 dark:border-gray-600 dark:text-gray-300"
					aria-label={$i18n.t(toolLabels[tool].label)}
					value={currentMode(tool)}
					on:change={(e) => setMode(tool, e.currentTarget.value as Mode)}
				>
					{#each modeOptions as mode}
						<option value={mode}
							>{$i18n.t(mode === 'auto' ? 'Auto' : mode === 'manual' ? 'Manual' : 'Off')}</option
						>
					{/each}
				</select>
			</div>
		{/each}
	</div>
</div>
