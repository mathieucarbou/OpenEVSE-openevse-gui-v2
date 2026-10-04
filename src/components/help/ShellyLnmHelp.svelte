<script>
	import {_, format} from 'svelte-i18n'
</script>
<style>
	.content {
		white-space: pre-line;
	}
</style>
<div>
	<p class="content">
		{@html $_('config.shellylnm.help-main')}
		<br><br>
		{$_('config.shellylnm.help-1')}
		<br>
		{$_('config.shellylnm.help-2')}
		<br>
		{$_('config.shellylnm.help-3')}
		<br><br>
		{@html $_('config.shellylnm.help-setup',
			{values: {addr: "239.255.55.55", port: "5555"}}
		)}
	</p>
</div>