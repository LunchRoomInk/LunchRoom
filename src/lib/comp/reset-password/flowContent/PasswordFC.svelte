<script lang="ts">
	import type { uniqueSignupProcessStatus } from "$route/signup/+page.svelte";
	import { createEventDispatcher } from "svelte";
	import LoadingDots from "./../../ui/general/LoadingDots.svelte";
	import PasswordVisibleToggle from "./../../ui/general/PasswordVisibleToggle.svelte";

	export let input: HTMLInputElement;
	export let value: string;
	export let status: uniqueSignupProcessStatus;
	let contentHeight: number;

	const disp = createEventDispatcher();

	let passwordRequirements: Record<number, boolean>;
	$: passwordRequirements = {
		0: (value?.length || 0) >= 6 && (value?.length || 0) <= 30,
		1: /\d/.test(value),
		2: /[A-Z]/.test(value),
		3: /[!@#$%^&*()'"=`_+{}\[\]\/:;<>,.?~\\|-]/.test(value)
	};

	let failedLatched = false;
	$: failedLatched = failedLatched || status.state === "failed";

	$: passwordValid =
		passwordRequirements[0] &&
		passwordRequirements[1] &&
		passwordRequirements[2] &&
		passwordRequirements[3];

	let passwordVisible = false;

	const onSubmit = () => {
		disp("submit");
	};
</script>

<main bind:clientHeight={contentHeight}>
	<h1>Choose a new password.</h1>
	{#if failedLatched}
		<p id="failure-error">{status.message}</p>
	{:else}
		<p>And remember it this time :3</p>
	{/if}

	<div id="input-container">
		{#if passwordVisible}
			<input
				bind:this={input}
				bind:value
				placeholder="•••••••••"
				type="text"
				class="input-field large hide-placeholder"
			/>
		{:else}
			<input
				bind:this={input}
				bind:value
				placeholder="•••••••••"
				type="password"
				class="input-field large hide-placeholder"
			/>
		{/if}

		<div id="toggle-visible">
			<PasswordVisibleToggle bind:passwordVisible />
		</div>
	</div>

	<button
		disabled={!passwordValid || status.state === "processing"}
		class="solid large {!passwordValid ? 'locked' : ''}"
		id="submit"
		type="submit"
		on:click={onSubmit}
	>
		{#if status.state === null || status.state === "failed"}
			Reset Password
		{:else}
			<LoadingDots />
		{/if}
	</button>

	<div id="message-container" style="transform: translateY({contentHeight / 2 + 100}px)">
		<div class="pwd-req">
			<div class="pwd-check-status {passwordRequirements[0] ? 'ok' : ''}" />
			<h6 class="exclude-phone">Make it between 6 and 30 characters long</h6>
			<h6 class="only-phone">Between 6 and 30 characters long</h6>
		</div>
		<div class="pwd-req">
			<div class="pwd-check-status {passwordRequirements[1] ? 'ok' : ''}" />
			<h6 class="exclude-phone">Include at least 1 number</h6>
			<h6 class="only-phone">Have at least 1 number</h6>
		</div>
		<div class="pwd-req">
			<div class="pwd-check-status {passwordRequirements[2] ? 'ok' : ''}" />
			<h6 class="exclude-phone">Include at least 1 capitcalized letter</h6>
			<h6 class="only-phone">Have at least 1 capitcalized letter</h6>
		</div>
		<div class="pwd-req">
			<div class="pwd-check-status {passwordRequirements[3] ? 'ok' : ''}" />
			<h6 class="exclude-phone">Include at least 1 special character (!@#$%^&*\)</h6>
			<h6 class="include-phone">Have at least 1 special character</h6>
		</div>
	</div>
</main>

<style lang="scss">
	@import "$static/stylesheets/guideline";

	main {
		width: 100%;
		position: relative;
		display: flex;
		justify-content: center;
		align-items: center;
		flex-direction: column;

		h1 {
			margin-bottom: 8px;
			text-align: center;
		}

		p {
			font-size: 16px;
			margin-bottom: 28px;
		}

		#failure-error {
			color: $red;
			font-size: 16px;
			margin-bottom: 28px;
		}

		div {
			width: 100%;
			height: fit-content;
			position: relative;

			display: flex;
			justify-content: center;
			align-items: center;
		}

		#input-container {
			max-width: 600px;

			input {
				width: 100%;
				text-align: center;

				padding-right: 44px !important;
				padding-left: 44px !important;
				box-sizing: border-box;

				&::-webkit-contacts-auto-fill-button {
					visibility: hidden;
					display: none !important;
					pointer-events: none;
					width: 0 !important;
					margin: 0 !important;
				}
			}

			#toggle-visible {
				position: absolute;
				width: fit-content;
				height: fit-content;

				right: 0px;
			}
		}

		#submit {
			margin-top: 30px;
			width: fit-content;
			min-width: 80px;

			transition: opacity 200ms ease-in-out;

			&:disabled {
				pointer-events: none;
				cursor: default;
			}

			&.locked {
				opacity: 0.3;
			}
		}

		#message-container {
			position: absolute;

			width: fit-content;
			display: flex;
			justify-content: center;
			align-items: center;
			flex-direction: column;

			.pwd-req {
				display: flex;
				justify-content: flex-start;
				margin-bottom: 8px;

				&:last-child {
					margin: 0;
				}

				.pwd-check-status {
					width: 8px;
					height: 8px;
					border-radius: 100%;
					background-color: $pentinary;
					margin-right: 14px;

					transition: background-color 200ms ease-in-out;

					&.ok {
						background-color: $black;
					}
				}
			}

			h6 {
				font-size: 16px;
				position: relative;
				color: $tertiary;
			}
		}

		@media screen and (max-width: $mobile-width) {
			h1 {
				font-size: 32px;
			}
		}
	}
</style>
