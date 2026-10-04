<script>
	import Borders 				from "./../../ui/Borders.svelte";
	import { onMount } 			from "svelte";
	import { _ } 			    from 'svelte-i18n'
	import { status_store } 	from "./../../../lib/stores/status.js";
	import { config_store } 	from "./../../../lib/stores/config.js";
	import { submitFormData,
			round } 			from "./../../../lib/utils.js"
	import InputForm 			from "../../ui/InputForm.svelte";
	import Select 				from "../../ui/Select.svelte";
	import Box 					from "../../ui/Box.svelte";
	import Switch 				from "../../ui/Switch.svelte";
	import ShellyLnmHelp		from "../../help/ShellyLnmHelp.svelte";

	let mounted = false

	let power_fields = [
		{name: "act_power (Shelly EM / EM Mini)", value: "act_power"},
		{name: "total_act_power (Shelly 3EM total)", value: "total_act_power"},
		{name: "a_act_power (Shelly 3EM phase A)", value: "a_act_power"},
		{name: "b_act_power (Shelly 3EM phase B)", value: "b_act_power"},
		{name: "c_act_power (Shelly 3EM phase C)", value: "c_act_power"}
	]

	let voltage_fields = [
		{name: "voltage (Shelly EM / EM Mini)", value: "voltage"},
		{name: "a_voltage (Shelly 3EM phase A)", value: "a_voltage"},
		{name: "b_voltage (Shelly 3EM phase B)", value: "b_voltage"},
		{name: "c_voltage (Shelly 3EM phase C)", value: "c_voltage"},
	]

	let formdata = {
		shelly_lnm_enabled:         {val: false, input: undefined, status: "", req: true},
		shelly_lnm_addr:            {val: "",    input: undefined, status: "", req: true},
		shelly_lnm_port:            {val: 5555,  input: undefined, status: "", req: true},
		shelly_lnm_power_field:     {val: "",    input: undefined, status: "", req: true},
		shelly_lnm_voltage_field:   {val: "",    input: undefined, status: "", req: true}
	}

	let updateFormData = () => {
		formdata.shelly_lnm_enabled.val          = $config_store.shelly_lnm_enabled
		formdata.shelly_lnm_addr.val             = $config_store.shelly_lnm_addr
		formdata.shelly_lnm_port.val             = $config_store.shelly_lnm_port
		formdata.shelly_lnm_power_field.val      = $config_store.shelly_lnm_power_field
		formdata.shelly_lnm_voltage_field.val    = $config_store.shelly_lnm_voltage_field
	}

	let toggleShelly = async () => {
		await submitFormData({form: formdata, prop_enable: "shelly_lnm_enabled", i18n_path: "config.shellylnm.missing-"})
	}

	let setProperty = async (prop) => {
		await submitFormData({prop: prop, form: formdata, prop_enable: "shelly_lnm_enabled", i18n_path: "config.shellylnm.missing-"})
	}

	onMount(() => {
		updateFormData()
		mounted = true
	})
</script>

{#if mounted}
<Box title={$_("config.titles.shellylnm")} icon="fa6-solid:plug-circle-bolt" back={true} has_help={true}>
	<div slot="help"><ShellyLnmHelp /></div>
	<div class="columns is-centered">
		<div class="column is-three-quarters is-full-mobile">
			<div class="mb-2 is-flex is-align-items-center is-justify-content-center">
				<Borders classes={formdata.shelly_lnm_enabled.val?"has-background-primary-light":"has-background-light"}>
					<Switch
						name="shellylnmswitch"
						label={$_("enable")}
						onChange={toggleShelly}
						bind:this={formdata.shelly_lnm_enabled.input}
						bind:checked={formdata.shelly_lnm_enabled.val}
						bind:status={formdata.shelly_lnm_enabled.status}
						disabled={formdata.shelly_lnm_enabled.status == "loading"}
					/>

					{#if formdata.shelly_lnm_enabled.val}
					<div class="pb-1 my-3">
						<div class="is-size-7 {$status_store.shelly_lnm_listening?"has-text-info":"has-text-danger"}">
							{$status_store.shelly_lnm_listening?$_("config.shellylnm.listening"):$_("config.shellylnm.notlistening")}
						</div>
						<span class="is-size-7 has-text-weight-bold has-text-dark">
							{$_("config.shellylnm.lastupdated")}:
							<span class="{$status_store.shelly_lnm_data_age > 10000?"has-text-danger":$status_store.shelly_lnm_data_age <= 5000?"has-text-primary":"has-text-orange"}">
								{$status_store.shelly_lnm_data_age == 4294967295?"—":$status_store.shelly_lnm_data_age + " ms"}
							</span>
						</span>
					</div>
					{/if}
				</Borders>
			</div>

			<div class="is-size-7 mb-2 has-text-centered">{$_("config.shellylnm.desc")}</div>
			<div class="is-flex is-justify-content-center">
				<Borders grow>
					<div class="mb-2">
						<InputForm
							title="{$_("config.shellylnm.addr")}*"
							bind:this={formdata.shelly_lnm_addr.input}
							bind:value={formdata.shelly_lnm_addr.val}
							bind:status={formdata.shelly_lnm_addr.status}
							disabled={formdata.shelly_lnm_addr.status =="loading"}
							placeholder="239.255.55.55"
							onChange={()=>setProperty("shelly_lnm_addr")}
						/>
						<div class="is-size-7 has-text-left">{$_("config.shellylnm.addr-desc")}</div>
					</div>
					<div class="mb-2">
						<InputForm
							title="{$_("config.shellylnm.port")}*"
							bind:this={formdata.shelly_lnm_port.input}
							type="number"
							bind:value={formdata.shelly_lnm_port.val}
							bind:status={formdata.shelly_lnm_port.status}
							min=1024 max=65535 step=1
							disabled={formdata.shelly_lnm_port.status =="loading"}
							placeholder="5555"
							onChange={()=>setProperty("shelly_lnm_port")}
						/>
						<div class="is-size-7 has-text-left">{$_("config.shellylnm.port-desc")}</div>
					</div>
					<div class="mb-2">
						<Select
							title={$_("config.shellylnm.powerfield")}
							bind:value={formdata.shelly_lnm_power_field.val}
							items={power_fields}
							onChange={()=>setProperty("shelly_lnm_power_field")}
						/>
						<div class="is-size-7 has-text-left">{$_("config.shellylnm.powerfield-desc")}</div>
					</div>
					<div class="mb-2">
						<Select
							title={$_("config.shellylnm.voltagefield")}
							bind:value={formdata.shelly_lnm_voltage_field.val}
							items={voltage_fields}
							onChange={()=>setProperty("shelly_lnm_voltage_field")}
						/>
						<div class="is-size-7 has-text-left">{$_("config.shellylnm.voltagefield-desc")}</div>
					</div>
				</Borders>
			</div>
		</div>
	</div>
</Box>
{/if}