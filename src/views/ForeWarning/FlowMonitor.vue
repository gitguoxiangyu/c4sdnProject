<template>
    <div id="main">

    </div>
</template>

<script setup>
import { onMounted , ref } from "vue";
import * as echarts from 'echarts';
import devices from './devices.json'
import hosts from './hosts.json'
import links from './links.json'
import { useMainStore } from '@/stores/index'

let packetLossRate = 0.01
let outLoss

let store = useMainStore()
console.log(store)


// console.log(myChart);

onMounted(() => {
    let linkLines = []

    let devicesMap = new Map()
    let hostMap = new Map()
    // 处理device节点
    let deviceNodes = []
    console.log(devices);
    devices.devices.forEach(element => {
        devicesMap.set(element.annotations.name, element)
        let item = {
            nodeName: element.annotations.name,
            value: [element.annotations.gridX, element.annotations.gridY],
            symbolSize: 40,
            symbol: 'path://M833.8 554.4c-11.5-17.9-26.7-28.5-44.7-40.1-4.1-2.6-14.6-10.2-18.9-12.6-18.5-9.9-38-15.9-54.1-19.5-0.1-0.3-0.1-0.5-0.1-0.7-4.7-1-25.9-4.4-29.8-5-2.2-18-15.4-39.8-26.6-53.6-11.2-13.8-34.1-32.3-53.2-40.7-19.1-8.4-33.3-11-40.1-12.6-12.1-2.2-25.3-1.8-40-1.8-29.4 0-55.8 7.9-78.6 23.5-19 13-39 25.2-51.7 47.8-12.4 22.1-15.7 47.1-19.6 66.3-17.2 3.2-39.6 9.5-61.8 22-22.7 12.7-31.6 21.1-41.9 33.7-17.6-21-26.5-49-26.5-83.2 0-31.7 12.7-53.6 39.8-68.8 24.7-13.9 52.6-16.9 61.3-16.9 19.1 0 34.7-17.4 34.7-38.8 0-8.8 0.3-39.7 13.2-62.6 15.4-27.4 41.5-36.8 71.6-36.8 37.1 0 65.5 10.4 82.3 30 12.4 14.6 15.2 30.5 15.3 34 0 14.9 7.5 27.8 18.5 34.3 6.9 1.2 13.5 2.8 19.7 4.6 9.7 0.5 37.9 3.2 64.1 17.3 32 17.2 48 43.5 49.7 82.3 16.7 3.5 36.7 9.6 56.3 20.2 4.4 2.3 8.6 4.8 12.6 7.5 0.4-6.5 0.5-13.3 0.5-20.3 0-39.9-9.4-74.8-27.8-103.6-15-23.4-35.6-42.3-61.4-56.1-24.9-13.4-50.3-19.6-69-22.5-3.5-20.5-12.3-41.4-26.7-56.1-22.2-22.6-43.3-49.2-134.2-49.2-29.4 0-55.8 5-78.6 20.5-19 13-39 30.1-51.7 52.7-12.4 22.1-15.7 49-19.6 68.3-17.2 3.2-39.6 9.5-61.8 22-50.4 28.3-78.1 77.4-78.1 138.2 0 76.2 30.5 122.7 56.1 148.1 6.8 6.8 13.6 12.5 20.1 17.2v-0.4c19 13.7 34.6 18.8 36.8 19.6 3.1 1 6.3 1.5 9.6 1.5h0.1l19.7 0.4v-0.5h198.4c10.2 15 26 24 43.8 24 30.8 0 55.8-27.9 55.8-62.4s-25-62.4-55.8-62.4c-17.7 0-33.5 8.9-43.8 23.9H353.5c0.6 0 1.2-0.8 1.8-1.1 2.5-0.2 4.2-0.4 4.8-0.6 20.9-7.8 42.2-11.7 50.8-12.4 22.4-1.9 28.2-11.3 30.4-31.7 1-8.7 0.3-37.7 13.2-60.7 15.4-27.4 41.5-34.9 71.6-34.9 37.1 0 65.5 10.4 82.3 30 12.4 14.6 15.2 43.3 16 46.6 2.6 11.9 13.2 25.9 33.7 28 0.2 0 20.4 0.7 37.5 4.1-0.1 0.2-0.1 0.4-0.2 0.6 25 4.3 34.6 8.7 47.2 15.5 10.2 5.5 18.2 13.9 25.2 21.4 14.3 16.8 24.6 38.7 24.6 66.4 0 52.4-12.7 70.4-37.7 89.7-18.1 13.9-37.3 14.9-39 14.9h-0.4l-332.8 0.7c-8.6 0-24.7-3.4-36.2-15.4-17.7-18.6-21-31.9-23.5-61.9l-22.3 0.1h-0.1c-3.2 0-6.5-0.7-9.6-1.7-2.3-0.7-18.4-0.9-37.8-15.1-0.1 2.4-0.1 3.7-0.1 6.2 0 76.2 19.7 106.7 45.3 132.1 27.3 27.2 61.8 31 64.8 31.9 3.1 1 10.2 1.5 13.5 1.5h0.1l338.8-1h0.3c6.8 0 42.3-1.2 77.1-27.5 31.5-23.7 69-54.6 69-154.6-0.2-40.2-9.6-75-28-103.8z',
            itemStyle: {
                color: 'orange',
            }
        }
        deviceNodes.push(item)

    });
    // 处理host节点
    let hostNodes = []
    hosts.hosts.forEach(element => {
        hostMap.set(element.annotations.name, element)
        let item = {
            nodeName: element.annotations.name,
            value: [element.annotations.gridX, element.annotations.gridY],
            symbolSize: 30,
            symbol: 'path://M232.727 139.636h558.546a93.09 93.09 0 0 1 93.09 93.091v372.364a93.09 93.09 0 0 1-93.09 93.09H232.727a93.09 93.09 0 0 1-93.09-93.09V232.727a93.09 93.09 0 0 1 93.09-93.09z m0 46.546a46.545 46.545 0 0 0-46.545 46.545v372.364a46.545 46.545 0 0 0 46.545 46.545h558.546a46.545 46.545 0 0 0 46.545-46.545V232.727a46.545 46.545 0 0 0-46.545-46.545H232.727z m256 512h46.546v139.636h-46.546V698.182zM279.273 837.818h465.454a23.273 23.273 0 0 1 0 46.546H279.273a23.273 23.273 0 0 1 0-46.546z',
            itemStyle: {
                color: 'orange',
            }
        }
        hostNodes.push(item)

        let line = {}
        let connectDevice = devicesMap.get(element.locations[0].elementId)

        line.coords = [[connectDevice.annotations.gridX, connectDevice.annotations.gridY], [element.annotations.gridX, element.annotations.gridY]]
        linkLines.push(line)
    })

    // 处理连接线
    links.links.forEach(element => {
        let { src, dst, state } = element

        let srcDevice = devicesMap.get(src.device)
        let dstDevice = devicesMap.get(dst.device)

        let line = {}
        line.coords = [[srcDevice.annotations.gridX, srcDevice.annotations.gridY], [dstDevice.annotations.gridX, dstDevice.annotations.gridY]]
        linkLines.push(line)
    })

    // 获取dom操作不能放在 setup 内，该生命周期钩子调用时，dom还未挂载，会导致dom无法正常获取
    // 基于准备好的dom，初始化echarts实例

    let option = {
        backgroundColor: "#W0B1321",
        xAxis: {
            min: 0,
            max: 1200,
            show: false,
            type: 'value'
        },
        yAxis: {
            min: 0,
            max: 1200,
            show: false,
            type: 'value'
        },
        tooltip: {},
        series: [
            // 节点
            {
                type: 'graph',
                coordinateSystem: 'cartesian2d',
                label: {
                    show: true,
                    position: 'bottom',
                    color: 'orange',
                    formatter: function (item) {
                        return item.data.nodeName
                    }
                },
                tooltip:{

                },
                data: deviceNodes
            },
            {
                type: 'graph',
                coordinateSystem: 'cartesian2d',
                label: {
                    show: true,
                    position: 'bottom',
                    color: 'orange',
                    formatter: function (item) {
                        return item.data.nodeName
                    }
                },
                data: hostNodes
            },
            // 线
            {
                type: 'lines',
                polyline: true,
                coordinateSystem: 'cartesian2d',

                lineStyle: {
                    type: 'dashed',
                    width: 2,
                    color: '#175064',
                    curveness: 0.3

                },
                effect: {
                    show: true,
                    trailLength: 0.1,
                    symbol: 'arrow',
                    color: 'orange',
                    symbolSize: 8
                },
                tooltip:{
                    formatter: ()=>{
                        return `丢包率：${packetLossRate}%`
                    }
                },
                data: linkLines
            },
        ]
    };

    var myChart = echarts.init(document.getElementById('main'));
    // 绘制图表
    myChart.setOption(option);
    console.log(store.isChange);
    if(store.isChange){
        console.log('bbb');
        console.log(store.host);
        outLoss = packetLossRate + 1
        let host = hostMap.get(store.host)
        let dst = devicesMap.get(store.dst)
        let line = {}
        console.log(dst);
        line.coords = [[host.annotations.gridX, host.annotations.gridY], [dst.annotations.gridX, dst.annotations.gridY]]
        let redLine = [line]
        option.series.push(// 线
        {
            type: 'lines',
            polyline: true,
            coordinateSystem: 'cartesian2d',

            lineStyle: {
                type: 'dashed',
                width: 2,
                color: 'red',
                curveness: 0.3

            },
            effect: {
                show: true,
                trailLength: 0.1,
                symbol: 'arrow',
                color: 'red',
                symbolSize: 8
            },
            tooltip:{
                formatter: ()=>{
                    return `丢包率：${outLoss}%`
                }
            },
            data: redLine
        })
        let timer = setInterval(()=>{

            outLoss -= 0.03
            if (outLoss < 0.8) {
                // console.log("<0.8");
                // console.log(option.series[option.series.length - 1]);
                option.series[option.series.length - 1].lineStyle.color = '#175064'
                option.series[option.series.length - 1].effect.color = 'orange'
                option.series[option.series.length - 1].tooltip.formatter = ()=>{
                    return `<div>丢包率：${packetLossRate}%</div>
                    <div>{
                        "slices": [
                            {
                            "slice_id": 1,
                            "minRate": 205,
                            "maxRate": 892,
                            "flows": [{
                                "src_ip": "2001:1:1::a",
                                "dst_ip": "2001:2:3::1"
                            }]
                        },
                        <br>
                        {
                            "slice_id": 2,
                            "minRate": 1024,
                            "maxRate": 1024,
                            "flows": [{
                                "next_hdr": "TYPE_UDP"
                            }]
                        }
                    ]
                    }</div>
                    `
                }
                clearInterval(timer)
            }
        },1000)

    }
    setInterval(()=>{
        packetLossRate = Math.random()%2/10
        myChart.setOption(option)
    },1000)
    // setTimeout(()=>{
    //     let redLine = [linkLines[linkLines.length-1],linkLines[linkLines.length-2]]
        
        
        
    //     option.series.push({
    //         type: 'lines',
    //         polyline: true,
    //         coordinateSystem: 'cartesian2d',

    //         lineStyle: {
    //             type: 'dashed',
    //             width: 2,
    //             color: 'red',
    //             curveness: 0.3

    //         },
    //         effect: {
    //             show: true,
    //             trailLength: 0.1,
    //             symbol: 'arrow',
    //             color: 'red',
    //             symbolSize: 8
    //         },
    //         tooltip:{
    //             formatter: ()=>{
    //                 return `丢包率：${outLoss}%`
    //             }
    //         },
    //         data: redLine
    //     })
    //     myChart.setOption(option)
    // },3000)
})

</script>

<style scoped lang="less">
#main {
    height: 100%;
    width: 100%;
}
</style>