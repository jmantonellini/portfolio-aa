<!-- src/routes/camera-finder/+page.svelte -->
<script lang="ts">
	import { Canvas, T, useThrelte } from '@threlte/core';
	import { onMount } from 'svelte';
	import * as THREE from 'three';
	import Model from '$lib/components/Model.svelte';

	let captureCount = 0;
	let positions = [];
	let isLocked = false;

	// This component runs inside Canvas and controls the camera directly
	const FlyCamera = () => {
		const { camera } = useThrelte();

		// Movement state
		const keys = {
			w: false,
			a: false,
			s: false,
			d: false,
			space: false,
			shift: false
		};

		// Rotation state
		let yaw = 0;
		let pitch = 0;
		const speed = 0.2;
		const lookSpeed = 0.002;

		// Set up controls
		onMount(() => {
			// Initial camera position
			if (camera.current) {
				camera.current.position.set(0, 1.6, 3);
				camera.current.rotation.set(0, 0, 0);
			}

			// Keyboard handlers
			const handleKeyDown = (e: KeyboardEvent) => {
				switch (e.code) {
					case 'KeyW':
						keys.w = true;
						e.preventDefault();
						break;
					case 'KeyA':
						keys.a = true;
						e.preventDefault();
						break;
					case 'KeyS':
						keys.s = true;
						e.preventDefault();
						break;
					case 'KeyD':
						keys.d = true;
						e.preventDefault();
						break;
					case 'Space':
						keys.space = true;
						e.preventDefault();
						break;
					case 'ShiftLeft':
					case 'ShiftRight':
						keys.shift = true;
						e.preventDefault();
						break;
				}
			};

			const handleKeyUp = (e: KeyboardEvent) => {
				switch (e.code) {
					case 'KeyW':
						keys.w = false;
						e.preventDefault();
						break;
					case 'KeyA':
						keys.a = false;
						e.preventDefault();
						break;
					case 'KeyS':
						keys.s = false;
						e.preventDefault();
						break;
					case 'KeyD':
						keys.d = false;
						e.preventDefault();
						break;
					case 'Space':
						keys.space = false;
						e.preventDefault();
						break;
					case 'ShiftLeft':
					case 'ShiftRight':
						keys.shift = false;
						e.preventDefault();
						break;
				}
			};

			// Mouse movement
			const handleMouseMove = (e: MouseEvent) => {
				if (!isLocked) return;

				yaw -= e.movementX * lookSpeed;
				pitch -= e.movementY * lookSpeed;
				pitch = Math.max(-Math.PI / 2, Math.min(Math.PI / 2, pitch));

				if (camera.current) {
					camera.current.rotation.x = pitch;
					camera.current.rotation.y = yaw;
				}
			};

			// Pointer lock
			const handlePointerLockChange = () => {
				isLocked = document.pointerLockElement !== null;
				const status = document.getElementById('status');
				if (status) {
					status.textContent = isLocked
						? '🟢 Controls Active - Press C to capture'
						: '⚪ Click to activate';
				}
			};

			// Click canvas to activate
			const handleCanvasClick = () => {
				const canvas = document.querySelector('canvas');
				if (canvas) canvas.requestPointerLock();
			};

			// Animation loop
			let animationId: number;
			const animate = () => {
				if (camera.current && isLocked) {
					// Calculate movement direction
					const forward = new THREE.Vector3(0, 0, -1);
					const right = new THREE.Vector3(1, 0, 0);

					forward.applyEuler(new THREE.Euler(pitch, yaw, 0, 'YXZ'));
					right.applyEuler(new THREE.Euler(0, yaw, 0, 'YXZ'));

					// Apply movement
					if (keys.w) camera.current.position.addScaledVector(forward, speed);
					if (keys.s) camera.current.position.addScaledVector(forward, -speed);
					if (keys.d) camera.current.position.addScaledVector(right, speed);
					if (keys.a) camera.current.position.addScaledVector(right, -speed);
					if (keys.space) camera.current.position.y += speed;
					if (keys.shift) camera.current.position.y -= speed;
				}

				animationId = requestAnimationFrame(animate);
			};

			// Add event listeners
			window.addEventListener('keydown', handleKeyDown);
			window.addEventListener('keyup', handleKeyUp);
			window.addEventListener('mousemove', handleMouseMove);
			document.addEventListener('pointerlockchange', handlePointerLockChange);

			const canvas = document.querySelector('canvas');
			if (canvas) canvas.addEventListener('click', handleCanvasClick);

			animate();

			return () => {
				window.removeEventListener('keydown', handleKeyDown);
				window.removeEventListener('keyup', handleKeyUp);
				window.removeEventListener('mousemove', handleMouseMove);
				document.removeEventListener('pointerlockchange', handlePointerLockChange);
				if (canvas) canvas.removeEventListener('click', handleCanvasClick);
				cancelAnimationFrame(animationId);
			};
		});

		// Capture function exposed to window
		if (typeof window !== 'undefined') {
			(window as any).captureCameraPosition = () => {
				if (!camera.current) return;

				const pos = camera.current.position;
				const dir = new THREE.Vector3(0, 0, -1);
				dir.applyQuaternion(camera.current.quaternion);

				const position = [pos.x, pos.y, pos.z];
				const target = [pos.x + dir.x * 5, pos.y + dir.y * 5, pos.z + dir.z * 5];

				captureCount++;
				positions.push({ position, target });

				// Update UI
				const countEl = document.getElementById('count');
				if (countEl) countEl.textContent = captureCount.toString();

				const listEl = document.getElementById('list');
				if (listEl) {
					const item = document.createElement('div');
					item.className = 'bg-gray-800 p-2 rounded text-xs';
					item.innerHTML = `
            <div class="text-yellow-300 font-bold">Spot ${captureCount}</div>
            <div class="text-gray-300">
              pos: [${position.map((v) => v.toFixed(2)).join(', ')}]<br>
              target: [${target.map((v) => v.toFixed(2)).join(', ')}]
            </div>
          `;
					listEl.appendChild(item);
				}

				// Copy to clipboard
				const output = `{
  position: [${position.map((v) => v.toFixed(2)).join(', ')}],
  target: [${target.map((v) => v.toFixed(2)).join(', ')}]
}`;

				navigator.clipboard?.writeText(output);
				console.log(`\n=== SPOT ${captureCount} ===\n${output}`);

				if (captureCount === 5) {
					console.log('\n🎉 All spots captured! Full array:\n', positions);
				}
			};
		}

		return () => null;
	};

	// Handle C key press
	function handleKeyPress(e: KeyboardEvent) {
		if (e.key === 'c' || e.key === 'C') {
			(window as any).captureCameraPosition?.();
		}
	}
</script>

<svelte:window on:keydown={handleKeyPress} />

<!-- Simple UI -->
<div class="pointer-events-none fixed inset-0 z-50">
	<div class="pointer-events-auto absolute top-4 left-4">
		<div class="rounded-lg bg-black/80 p-4 text-white">
			<h3 class="mb-2 font-bold">🎮 Fly Controls</h3>
			<div class="space-y-1 text-sm">
				<p><kbd class="rounded bg-gray-700 px-2 py-0.5">WASD</kbd> - Move</p>
				<p><kbd class="rounded bg-gray-700 px-2 py-0.5">Space</kbd> - Up</p>
				<p><kbd class="rounded bg-gray-700 px-2 py-0.5">Shift</kbd> - Down</p>
				<p><kbd class="rounded bg-gray-700 px-2 py-0.5">Mouse</kbd> - Look</p>
				<p class="mt-2 text-yellow-300">
					<kbd class="rounded bg-yellow-600 px-2 py-0.5">C</kbd> - Capture
				</p>
			</div>
		</div>
	</div>

	<div class="pointer-events-auto absolute top-4 right-4">
		<div class="rounded-lg bg-black/80 p-4 text-white">
			<p>Captured: <span id="count">0</span>/5</p>
			<div id="list" class="mt-2 space-y-1"></div>
		</div>
	</div>

	<div class="pointer-events-auto absolute bottom-4 left-1/2 -translate-x-1/2">
		<div class="rounded-full bg-black/60 px-4 py-2 text-white backdrop-blur-sm">
			<span id="status">⚪ Click to activate</span>
		</div>
	</div>
</div>

<div class="h-screen w-full">
	<Canvas>
		<!-- Fly Camera MUST be first to take control -->
		<FlyCamera />

		<!-- Your model -->
		<Model />

		<!-- Lighting -->
		<T.AmbientLight intensity={0.5} />
		<T.DirectionalLight position={[5, 10, 5]} intensity={1} />

		<!-- Helpers -->
		<T.GridHelper args={[20, 20]} />
		<T.AxesHelper args={[5]} />
	</Canvas>
</div>

<style>
	:global(body) {
		margin: 0;
		padding: 0;
		overflow: hidden;
	}

	kbd {
		background: #333;
		border-radius: 3px;
		padding: 2px 6px;
		font-family: monospace;
	}
</style>
