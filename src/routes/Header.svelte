<script>
	import logo from '$lib/images/logo.svg';
</script>

<header class="header">
	<div class="container">
		<a href="#" class="header__logo">
			<img src="{logo}" alt="Coffee API Client" class="header__logo-pic">
			<span class="header__logo-text">Coffee API Client</span>
		</a>
	</div>
</header>

<style>
	.header {
		padding: 12px 0;
		box-shadow: 0 3px 6px rgba(0,0,0,0.16);
	}
	.header__logo {
		display: inline-block;
	}
	.header__logo-pic {
		max-width: 50px;
		display: inline-block;
		vertical-align: middle;
		margin-right: 15px;
	}
	.header__logo-text {
		font-weight: 900;
		letter-spacing: 1px;
		font-size: 20px;
		display: inline-block;
		vertical-align: middle;
		text-transform: uppercase;
	}
	@media screen and (max-width: 767px) {
		.header {
			padding: 8px 0;
		}
		.header__logo-pic {
			max-width: 40px;
		}
		.header__logo-text {
			font-size: 18px;
		}
	}
</style>
