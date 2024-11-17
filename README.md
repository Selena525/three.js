<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Juego de Disparos</title>
    <style>
        body { margin: 0; overflow: hidden; }
        canvas { display: block; }
    </style>
</head>
<body>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/three.js/r128/three.min.js"></script>
    <script>
        let scene, camera, renderer;
        let raycaster, mouse;
        let targets = [];
        let clock = new THREE.Clock();

        // Crear la escena, cámara y renderizador
        scene = new THREE.Scene();
        camera = new THREE.PerspectiveCamera(75, window.innerWidth / window.innerHeight, 0.1, 1000);
        renderer = new THREE.WebGLRenderer();
        renderer.setSize(window.innerWidth, window.innerHeight);
        document.body.appendChild(renderer.domElement);

        // Añadir luz
        const light = new THREE.AmbientLight(0x404040); // Luz ambiental
        scene.add(light);

        // Crear raycaster y mouse
        raycaster = new THREE.Raycaster();
        mouse = new THREE.Vector2();

        // Función para crear muñecos como objetos
        function createTarget() {
            const targetGeometry = new THREE.SphereGeometry(0.5, 32, 32);
            const targetMaterial = new THREE.MeshBasicMaterial({ color: 0xff0000 });
            const target = new THREE.Mesh(targetGeometry, targetMaterial);
            target.position.set(Math.random() * 20 - 10, 1, Math.random() * 20 - 10);
            targets.push(target);
            scene.add(target);
        }

        // Crear varios muñecos
        for (let i = 0; i < 5; i++) {
            createTarget();
        }

        // Función para controlar el ratón
        function onMouseMove(event) {
            mouse.x = (event.clientX / window.innerWidth) * 2 - 1;
            mouse.y = - (event.clientY / window.innerHeight) * 2 + 1;
        }

        // Función de disparo (Raycast)
        function shoot() {
            // Convertir las coordenadas del mouse a coordenadas de la escena 3D
            raycaster.update(camera.position, camera.getWorldDirection(new THREE.Vector3()));

            // Detectar los objetos que el raycast está tocando
            const intersects = raycaster.intersectObjects(targets);
            if (intersects.length > 0) {
                intersects[0].object.material.color.set(0x00ff00); // Cambiar color a verde
                scene.remove(intersects[0].object); // Eliminar el muñeco
                targets.splice(targets.indexOf(intersects[0].object), 1); // Eliminar de la lista de objetivos
                createTarget(); // Crear un nuevo muñeco
            }
        }

        // Eventos de entrada (movimiento y disparo)
        window.addEventListener('mousemove', onMouseMove, false);
        window.addEventListener('click', shoot, false);

        // Animación de la escena
        function animate() {
            requestAnimationFrame(animate);

            // Actualizar el raycaster para que apunte desde la cámara hacia la dirección del mouse
            raycaster.update(camera.position, camera.getWorldDirection(new THREE.Vector3()));

            // Animar los muñecos
            targets.forEach(target => {
                target.position.y = Math.sin(clock.getElapsedTime() + target.position.x) + 1;
            });

            renderer.render(scene, camera);
        }

        // Ajustar el tamaño del canvas
        window.addEventListener('resize', () => {
            renderer.setSize(window.innerWidth, window.innerHeight);
            camera.aspect = window.innerWidth / window.innerHeight;
            camera.updateProjectionMatrix();
        });

        // Configurar la cámara
        camera.position.set(0, 2, 5);
        camera.lookAt(0, 1, 0);

        // Iniciar animación
        animate();
    </script>
</body>
</html>


