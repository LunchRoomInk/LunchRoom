<script lang="ts">
</script>

<h1>Your password has been reset.</h1>
<p>Try signing in again with your new password.</p>

<a href="/signin">
	<button class="solid large">Sign In</button>
</a>

<style lang="scss">
	@import "$static/stylesheets/guideline";

	h1 {
		margin-bottom: 10px;
		text-align: center;
	}

	p {
		font-size: 16px;
	}

	a {
		width: fit-content;
		height: fit-content;
		padding: 0;
		margin: 0;

		position: absolute;
		bottom: -80px;
	}

	@media screen and (max-width: $mobile-width) {
		h1 {
			font-size: 32px;
		}

		a {
			width: 100%;
			height: 46px;
			button {
				width: inherit;
				height: inherit;
			}
		}
	}
</style>
