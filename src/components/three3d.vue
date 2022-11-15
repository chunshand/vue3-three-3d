<template>
    <div class="three3d" ref="canvas">
    </div>
</template>
<script setup lang="ts">
import * as THREE from "three"
import { OrbitControls } from "three/examples/jsm/controls/OrbitControls"
import { GLTFLoader } from "three/examples/jsm/loaders/GLTFLoader"
import { RGBELoader } from "three/examples/jsm/loaders/RGBELoader"
import { onMounted, ref } from "vue";
const canvas = ref();

/**
 * 创建场景
 */
const CreateScene = () => {
    const scene = new THREE.Scene();
    // scene.background = new THREE.Color("#f1707d")
    scene.background = new THREE.Color("#FFFFFF")
    return scene
}

/**
 * 创建摄像机
 */
const CreateCamera = () => {
    let innerWidth = canvas.value.offsetWidth;
    let innerHeight = canvas.value.offsetHeight;
    const camera = new THREE.PerspectiveCamera(75, innerWidth / innerHeight, 0.1, 1000);
    camera.position.z = 10;
    camera.position.y = 5;
    return { camera, innerWidth, innerHeight };
}


/**
 * 加载模型
 */

const CreateModel = (scene: THREE.Scene) => {
    const loader = new GLTFLoader();
    loader.load('/01.gltf', (gltf) => {
        const box = new THREE.Box3().setFromObject(gltf.scene)
        // const center = box.getCenter(new THREE.Vector3())
        // const size = box.getSize(new THREE.Vector3())
        // gltf.scene.position.y = -size.y / 2;
        scene.add(gltf.scene);
    })
}
/**
 * 加载hdr
 */
const CreateHdr = (scene: THREE.Scene, renderer: THREE.WebGLRenderer) => {
    const loader = new RGBELoader();

    loader.load('/bg2.hdr', function (texture, textureData) {
        texture.mapping = THREE.EquirectangularReflectionMapping;
        //将加载的材质texture设置给背景和环境
        scene.background = texture;
        scene.environment = texture;
    });

}
const handleInit = () => {

    const scene = CreateScene();
    const { camera, innerWidth, innerHeight } = CreateCamera();
    const renderer = new THREE.WebGLRenderer({ antialias: true, alpha: true });

    // 环境光
    // const light = new THREE.AmbientLight(0x404040, 1);
    const light = new THREE.AmbientLight(0xffffff, 1);
    scene.add(light);


    // 平行光(太阳)
    const directionalLight = new THREE.DirectionalLight(0xffffff, 2);
    scene.add(directionalLight);

    // 聚光
    // const spotLight = new THREE.SpotLight(0x03a9f4, 2, 100, Math.PI / 8);
    // spotLight.position.set(5, 5, 5);
    // const helper = new THREE.SpotLightHelper(spotLight, 2);
    // scene.add(spotLight);
    // scene.add(helper);

    // 盒子
    // const geometry = new THREE.BoxGeometry(1, 1, 1);
    // const material = new THREE.MeshPhysicalMaterial({ color: 0xf1707d });
    // const cube = new THREE.Mesh(geometry, material);
    // scene.add(cube);
    // https://blog.csdn.net/damadashen/article/details/125695648
    // 加载模型
    CreateModel(scene)
    // 加载hdr
    CreateHdr(scene, renderer);

    // // 平面
    // const plane_geometry = new THREE.PlaneGeometry(10, 10);
    // const plane_material = new THREE.MeshPhysicalMaterial({ color: 0xf1707d, side: THREE.DoubleSide });
    // const plane = new THREE.Mesh(plane_geometry, plane_material);
    // plane.rotation.x = -0.5 * Math.PI
    // scene.add(plane);


    // // 中心坐标
    const axesHelper = new THREE.AxesHelper(30);
    scene.add(axesHelper);


    // // 网格
    const size = 30;
    const divisions = 30;
    const gridHelper = new THREE.GridHelper(size, divisions);
    scene.add(gridHelper);



    // // 二位二次贝塞尔曲线
    // const curve = new THREE.QuadraticBezierCurve3(
    //     new THREE.Vector3(-10, 0, 0),
    //     new THREE.Vector3(20, 15, 0),
    //     new THREE.Vector3(10, 0, 0)
    // );

    // const points = curve.getPoints(60);
    // const _geometry = new THREE.BufferGeometry().setFromPoints(points);

    // const _material = new THREE.LineBasicMaterial({ color: 0xffffff });

    // const curveObject = new THREE.Line(_geometry, _material);
    // scene.add(curveObject);







    // 摄像机控制器交互 可鼠标拖动 滚轮交互

    renderer.setSize(innerWidth, innerHeight);
    const controls = new OrbitControls(camera, renderer.domElement)
    canvas.value.appendChild(renderer.domElement)
    function animate() {
        requestAnimationFrame(animate);
        controls.update();
        renderer.render(scene, camera);
    }
    animate();
}

onMounted(() => {
    handleInit();
})
</script>
<style>
.three3d {
    width: 100%;
    height: 100%;
}
</style>
