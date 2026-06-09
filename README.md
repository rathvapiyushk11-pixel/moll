let scene, camera, renderer;
let player;

let move = {
  forward: false,
  back: false,
  left: false,
  right: false
};

init();
animate();

function init() {
  scene = new THREE.Scene();
  scene.background = new THREE.Color(0x87ceeb);

  camera = new THREE.PerspectiveCamera(
    75,
    window.innerWidth / window.innerHeight,
    0.1,
    1000
  );

  renderer = new THREE.WebGLRenderer();
  renderer.setSize(window.innerWidth, window.innerHeight);
  document.body.appendChild(renderer.domElement);

  // LIGHT
  const light = new THREE.DirectionalLight(0xffffff, 1);
  light.position.set(5, 10, 5);
  scene.add(light);

  // FLOOR (supermarket floor)
  const floorGeo = new THREE.PlaneGeometry(50, 50);
  const floorMat = new THREE.MeshBasicMaterial({ color: 0xdddddd });
  const floor = new THREE.Mesh(floorGeo, floorMat);
  floor.rotation.x = -Math.PI / 2;
  scene.add(floor);

  // PLAYER (cube)
  const playerGeo = new THREE.BoxGeometry(1, 2, 1);
  const playerMat = new THREE.MeshBasicMaterial({ color: 0xff0000 });
  player = new THREE.Mesh(playerGeo, playerMat);
  player.position.y = 1;
  scene.add(player);

  camera.position.set(0, 5, 8);

  // SHELVES
  createShelf(5, 0, 5);
  createShelf(-5, 0, 5);
  createShelf(5, 0, -5);
  createShelf(-5, 0, -5);

  // controls
  document.addEventListener("keydown", onKeyDown);
  document.addEventListener("keyup", onKeyUp);

  window.addEventListener("resize", onResize);
}

function createShelf(x, y, z) {
  const geo = new THREE.BoxGeometry(2, 3, 1);
  const mat = new THREE.MeshBasicMaterial({ color: 0x8b4513 });
  const shelf = new THREE.Mesh(geo, mat);
  shelf.position.set(x, y + 1.5, z);
  scene.add(shelf);
}

function onKeyDown(e) {
  if (e.key === "w") move.forward = true;
  if (e.key === "s") move.back = true;
  if (e.key === "a") move.left = true;
  if (e.key === "d") move.right = true;
}

function onKeyUp(e) {
  if (e.key === "w") move.forward = false;
  if (e.key === "s") move.back = false;
  if (e.key === "a") move.left = false;
  if (e.key === "d") move.right = false;
}

function animate() {
  requestAnimationFrame(animate);

  // movement
  if (move.forward) player.position.z -= 0.1;
  if (move.back) player.position.z += 0.1;
  if (move.left) player.position.x -= 0.1;
  if (move.right) player.position.x += 0.1;

  // camera follow
  camera.position.x = player.position.x;
  camera.position.z = player.position.z + 8;
  camera.lookAt(player.position);

  renderer.render(scene, camera);
}

function onResize() {
  camera.aspect = window.innerWidth / window.innerHeight;
  camera.updateProjectionMatrix();
  renderer.setSize(window.innerWidth, window.innerHeight);
}
