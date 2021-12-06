<template>
  <div class="container">
    <div id="d3graph"></div>
  </div>
</template>

<script lang="ts">
import * as d3 from 'd3';
import { get, uniq, find } from 'lodash-es';

export default {
  msg: 'd3graph',
  data() {
    return {
      /**
       * Provide data which cannot be correctly parsed and processed.
       * Static data is temporarily used
       */
      nodes: [
        {
          id: '数澜医院', name: '数澜医院', color: '#CA63F5', x: 0.3212011310171421, y: 0.5013640125217536,
        }, {
          id: '水泥', name: '水泥', color: '#55CCCC', x: 0.0662939262612241, y: 0.6237089703989482,
        }, {
          id: '安全员', name: '安全员', color: '#CA63F5', x: 0.7643417862602012, y: 0.8669983965223007,
        }, {
          id: '施工员', name: '施工员', color: '#55CCCC', x: 0.6679490350819621, y: 0.43620519023811055,
        }, {
          id: '高温雨季安全专项', name: '高温雨季安全专项', color: '#55CCCC', x: 0.82076841096744695, y: 0.4871580922309455,
        }, {
          id: '中建八局', name: '中建八局', color: '#CA63F5', x: 0.6566095535018828, y: 0.7108407582448829,
        }, {
          id: '道路安全规范', name: '道路安全规范', color: '#AAAAFF', x: 0.5993312121643075, y: 0.35081757480169684,
        }, {
          id: '高支模安全应急预案', name: '高支模安全应急预案', color: '#55CCCC', x: 0.20279228095156054, y: 0.18537608384743733,
        }, {
          id: '安全专项方案', name: '安全专项方案', color: '#55CCCC', x: 0.7996968487187811, y: 0.3737280338110722,
        }, {
          id: '剪刀墙', name: '剪刀墙', color: '#55CCCC', x: 0.4361059084024217, y: 0.41484097867780423,
        }, {
          id: '山东济南', name: '山东济南', color: '#AAAAFF', x: 0.5702207436635575, y: 0.4943809823345643,
        }, {
          id: '智能建造', name: '智能建造', color: '#55CCCC', x: 0.5553759654803966, y: 0.90465708668557,
        }, {
          id: '数字建造', name: '数字建造', color: '#55CCCC', x: 0.6815802526413997, y: 0.14472942576007908,
        }, {
          id: '山东第一设计院', name: '山东第一设计院', color: '#55CCCC', x: 0.22875078860644105, y: 0.36222008020910557,
        }, {
          id: '山东第一监理公司', name: '山东第一监理公司', color: '#55CCCC', x: 0.3860952123947859, y: 0.7447015486449265,
        }, {
          id: '钢筋', name: '钢筋', color: '#55CCCC', x: 0.8380653416224637, y: 0.6268099168150139,
        }, {
          id: '百度', name: '百度', color: '#55CCCC', x: 0.2248461559358539, y: 0.6474649750112433,
        }, {
          id: '上海总部', name: '上海总部', color: '#55CCCC', x: 0.8742100068245729, y: 0.21135871848686252,
        }, {
          id: '中铁财智中心', name: '中铁财智中心', color: '#CA63F5', x: 0.35261655636966647, y: 0.231142919957653,
        }, {
          id: '模板工程', name: '模板工程', color: '#55CCCC', x: 0.42182929747224658, y: 0.5865797420018556,
        }, {
          id: '智能写作', name: '智能写作', color: '#AAAAFF', x: 0.12899670128141905, y: 0.8472399736952392,
        }, {
          id: '物联网', name: '物联网', color: '#55CCCC', x: 0.3388976674192894, y: 0.8585428819118202,
        }, {
          id: '设计图纸', name: '设计图纸', color: '#AAAAFF', x: 0.08669900068513181, y: 0.31833429472804275,
        }, {
          id: '数澜医院设计图', name: '数澜医院设计图', color: '#CA63F5', x: 0.9182236077939948, y: 0.7705622612244249,
        },
      ],
      links: [{ source: '数澜医院', target: '水泥' }, { source: '安全员', target: '施工员' }, { source: '高温雨季', target: '安全专项' }, { source: '中建八局', target: '道路安全规范' }, { source: '数澜医院', target: '安全员' }, { source: '数澜医院', target: '安全专项' }, { source: '数澜医院', target: '中建八局' }, { source: '中建八局', target: '安全员' }, { source: '中建八局', target: '施工员' }, { source: '数澜医院', target: '高支模' }, { source: '数澜医院', target: '安全应急预案' }, { source: '数澜医院', target: '安全专项方案' }, { source: '数澜医院', target: '剪刀墙' }, { source: '数澜医院', target: '山东济南' }, { source: '数澜医院', target: '智能建造' }, { source: '数澜医院', target: '数字建造' }, { source: '数澜医院', target: '山东第一设计院' }, { source: '数澜医院', target: '山东第一监理公司' }, { source: '数澜医院', target: '钢筋' }, { source: '高温雨季方案', target: '安全应急预案' }, { source: '雄安建筑项目', target: '安全应急预案' }, { source: '中建八局', target: '安全应急预案' }, { source: '中建八局', target: '山东第一设计院' }, { source: '中建八局', target: '百度' }, { source: '中建八局', target: '上海总部' }, { source: '中建八局', target: '中铁财智中心' }, { source: '中建八局', target: '数字建造' }, { source: '中建八局', target: '智能建造' }, { source: '中建八局', target: '物资盘点' }, { source: '中建八局', target: '安全应急预案' }, { source: '模板工程', target: '安全应急预案' }, { source: '中铁财智中心', target: '数字建造' }, { source: '中铁财智中心', target: '智能写作' }, { source: '百度', target: '物联网' }, { source: '中建八局', target: '物联网' }, { source: '山东第一设计院', target: '设计图纸' }, { source: '山东第一设计院', target: '数澜医院设计图' }],
      svg: null,
    };
  },
  mounted() {
    const {
      nodes, links,
    } = this;
    this.createGraph(
      {
        nodes,
        links,
      },
      {
        currenNode: this.currenNode,
        onNodeClick: this.nodeClick,
        onNodeMouseOver: this.nodeClick,
      },
    );
  },
  methods: {
    /** Transform the sample data into the data structures required for D3, unused */
    createGraph(
      {
        nodes: originNodes, // an iterable of node objects (typically [{id}, …])
        links: originLinks, // an iterable of link objects (typically [{source, target}, …])
      },
      {
        nodeStroke = '#fff', // node stroke color
        nodeStrokeWidth = 1.5, // node stroke width, in pixels
        nodeStrokeOpacity = 0.85, // node stroke opacity
        nodeRadius = 32, // node radius, in pixels
        noLinkNodeRadius = 16, // node radius of no link, in pixels
        linkStroke = '#fff', // link stroke color
        linkStrokeOpacity = 0.6, // link stroke opacity
        linkStrokeWidth = 2, // given d in links, returns a stroke width in pixels
        linkStrokeLinecap = 'round', // link stroke linecap
        width = 1200, // outer width, in pixels
        height = 1200, // outer height, in pixels
        radius = 250, // layout radius
        duration = 1200, // animation duration time(ms)
        showArrows = true, // whether to show end arrow
      } = {},
    ) {
      const heightLightNode = ['数澜医院', '中建八局'];
      const getArrowOffset = (x1, y1, x2, y2) => {
        const expandNodeRadius = nodeRadius + 6;
        let offsetX = 0;
        let offsetY = 0;
        if (Math.abs(x2 - x1) < 1) {
          return { offsetX: 0, offsetY: y2 > y1 ? expandNodeRadius : -expandNodeRadius };
        }
        if (Math.abs(y2 - y1) < 1) {
          return { offsetX: x2 > x1 ? expandNodeRadius : -expandNodeRadius, offsetY: 0 };
        }
        // 全部使用锐角，减小理解成本
        if (x2 > x1) {
          if (y2 < y1) {
            const tan = (y1 - y2) / (x2 - x1);
            offsetX = expandNodeRadius * Math.cos(Math.atan(tan));
            offsetY = -expandNodeRadius * Math.sin(Math.atan(tan));
          } else {
            const tan = (y2 - y1) / (x2 - x1);
            offsetX = expandNodeRadius * Math.cos(Math.atan(tan));
            offsetY = expandNodeRadius * Math.sin(Math.atan(tan));
          }
        } else if (y2 < y1) {
          const tan = (y1 - y2) / (x1 - x2);
          offsetX = -expandNodeRadius * Math.cos(Math.atan(tan));
          offsetY = -expandNodeRadius * Math.sin(Math.atan(tan));
        } else {
          const tan = (y2 - y1) / (x1 - x2);
          offsetX = -expandNodeRadius * Math.cos(Math.atan(tan));
          offsetY = expandNodeRadius * Math.sin(Math.atan(tan));
        }
        return { offsetX, offsetY };
      };
      const svg = d3
        .select('#d3graph')
        .append('svg')
        .attr('width', width)
        .attr('height', height)
        .attr('viewBox', [0, 0, width, height])
        .attr('style', 'max-width: 100%; height: auto; height: intrinsic;');
      this.svg = svg;
      // draw arrow marker
      const defs = svg.append('defs');
      const arrowPath = 'M2,2 L10,6 L2,10 L6,6 L2,2';
      const arrowMarker = defs
        .append('marker')
        .attr('id', 'arrow')
        .attr('markerUnits', 'strokeWidth')
        .attr('markerWidth', '12')
        .attr('markerHeight', '12')
        .attr('viewBox', '0 0 12 12')
        .attr('refX', 6)
        .attr('refY', 6)
        .attr('orient', 'auto');
      arrowMarker.append('path').attr('d', arrowPath).attr('fill', linkStroke);
      // radial color
      const radialColor = d3.scaleLinear().domain([0, 1.0]).range(['#A2C7CC', '#C9B6C6']);
      defs
        .append('radialGradient')
        .attr('id', 'gradient')
        .attr('cx', '50%') // not really needed, since 50% is the default
        .attr('cy', '50%') // not really needed, since 50% is the default
        .selectAll('stop')
        .data([
          { offset: '0%', color: radialColor(0) },
          { offset: '20%', color: radialColor(0.2) },
          { offset: '40%', color: radialColor(0.4) },
          { offset: '60%', color: radialColor(0.6) },
          { offset: '80%', color: radialColor(0.8) },
          { offset: '100%', color: radialColor(1) },
        ])
        .enter()
        .append('stop')
        .attr('offset', (d) => d.offset)
        .attr('stop-color', (d) => d.color);
      const nodePosition = {};
      const animateNodes = [];
      const animateTexts = [];
      const animateLinks = [];
      const animateLinkTexts = [];
      const nodeLength = originNodes.length - 1;
      const dagre = (360 / nodeLength) * (Math.PI / 180);
      const [centerX, centerY] = [width / 2, height / 2];
      const randomRadius = radius / 6;
      const randomCenterRadius = radius / 2;
      originNodes.forEach((node, index) => {
        const negative = Math.random();
        const offset = Math.random() * randomRadius;
        let randomCenterX = centerX;
        let randomCenterY = centerY;
        let nodeRandomRadius = radius;
        if (negative >= 0.5) {
          nodeRandomRadius -= offset;
        } else {
          nodeRandomRadius += offset;
        }
        if (index === 0) {
          if (Math.random() > 0.5) {
            randomCenterX += Math.random() * randomCenterRadius;
            randomCenterY += Math.random() * randomCenterRadius;
          } else {
            randomCenterX -= Math.random() * randomCenterRadius;
            randomCenterY -= Math.random() * randomCenterRadius;
          }
        }
        // nodePosition[node.id] = {
        //   x: index === 0 ? randomCenterX : centerX + nodeRandomRadius * Math.sin(radius * (index - 1)),
        //   y: index === 0 ? randomCenterY : centerY + nodeRandomRadius * Math.cos(radius * (index - 1)),
        // };
        // 使用固定位置
        nodePosition[node.id] = {
          x: width * node.x,
          y: height * node.y,
        };
      });
      /** Loop processing edge, need to draw to the lowest level */
      originLinks.forEach((link, index) => {
        const { source, target } = link;
        if (!nodePosition[source] || !nodePosition[target]) {
          return;
        }
        const { x, y } = nodePosition[source];
        const { x: targetX, y: targetY } = nodePosition[target];
        const { offsetX, offsetY } = showArrows ? getArrowOffset(x, y, targetX, targetY) : { offsetX: 0, offsetY: 0 };
        const tempLink = svg
          .append('g')
          .append('line')
          .attr('x1', x)
          .attr('y1', y)
          .attr('x2', x)
          .attr('y2', y)
          .attr('endX', targetX - offsetX)
          .attr('endY', targetY - offsetY)
          .attr('sourceId', source)
          .attr('targetId', target)
          .attr('animateEnd', 'false') // 会被转为 string，直接使用 string
          .attr('stroke', linkStroke)
          .attr('stroke-opacity', 0)
          .attr('stroke-width', typeof linkStrokeWidth !== 'function' ? linkStrokeWidth : null)
          .attr('stroke-linecap', linkStrokeLinecap);
        animateLinks.push(tempLink);
        const tempText = svg
          .append('g')
          .append('text')
          .attr('x', (x + targetX) / 2)
          .attr('y', (y + targetY) / 2)
          .attr('id', `${source}-${target}`)
          .attr('text-anchor', 'middle')
          .attr('dominant-baseline', 'middle')
          .attr('fill', 'rgba(0,0,0,.85)')
          .attr('fill-opacity', 0)
          .attr('font-size', 12)
          .text(link.type);
        animateLinkTexts.push(tempText);
      });
      /** Loop the nodes and prepare the animation */
      originNodes.forEach((node, index) => {
        const { x, y } = nodePosition[node.id];
        const hasLink = find(originLinks, (t) => t.source === node.name || t.target === node.name);
        const tempNode = svg
          .append('circle')
          .attr('r', 0)
          .attr('fill', node.color)
          .attr('id', node.id)
          .attr('stroke', heightLightNode.includes(node.name) ? 'url(#gradient)' : nodeStroke)
          .attr('cx', centerX)
          .attr('cy', centerY)
          .attr('endX', x)
          .attr('endY', y)
          .attr('endR', hasLink ? nodeRadius : noLinkNodeRadius)
          .attr('class', hasLink ? 'hasLink' : 'noLink')
          .attr('delay', (index + 1) * duration)
          .attr('stroke-opacity', 0)
          .attr('stroke-width', heightLightNode.includes(node.name) ? 12 : nodeStrokeWidth);
        animateNodes.push(tempNode);
        const tempText = svg
          .append('text')
          .attr('x', x)
          .attr('y', y)
          .attr('endX', x)
          .attr('endY', y)
          .attr('class', hasLink ? 'hasLink' : 'noLink')
          .attr('text-anchor', 'middle')
          .attr('dominant-baseline', 'middle')
          .attr('fill', '#fff')
          .attr('fill-opacity', 0)
          .attr('font-size', 14)
          .text(node.name);
        animateTexts.push(tempText);
      });

      /** start node animation */
      animateNodes.forEach((node, index) => {
        node
          .transition()
          .ease(d3.easeLinear)
          .duration(duration)
          // .delay(index * duration) // 依次展开
          // .delay(index === 0 ? 0 : duration) // 一次展开
          .attr('r', node.attr('endR'))
          .attr('cx', node.attr('endX'))
          .attr('cy', node.attr('endY'))
          .attr('stroke-opacity', nodeStrokeOpacity);
      });
      /** start node text animation */
      animateTexts.forEach((text, index) => {
        text
          .transition()
          .ease(d3.easeLinear)
          .duration(duration)
          // .delay((index + 1) * duration) // 依次展开
          .delay(index === 0 ? 0 : duration * 0.75) // 一次展开
          .attr('fill-opacity', 1);
      });
      // 以 duration 的时间间隔定时查询节点状态，如果边上的2个节点已经过度好，执行边动画
      let time = null;
      time = setInterval(() => {
        let hasAnimate = false;
        animateNodes.forEach((node) => {
          if (node.attr('r') < node.attr('endR')) {
            hasAnimate = true;
          }
        });
        if (!hasAnimate) {
          clearInterval(time);
          // animation loop
          const loopNode = svg.select(`#${heightLightNode[0]}`);
          const opacityLoopNodes = svg.selectAll('.noLink');
          if (loopNode) {
            const run = () => {
              loopNode
                .transition()
                .ease(d3.easeLinear)
                .duration(duration)
                .attr('r', loopNode.attr('endR') / 2)
                .transition()
                .ease(d3.easeLinear)
                .duration(duration)
                .attr('r', loopNode.attr('endR'))
                .on('end', run);
            };
            run();
          }
          if (opacityLoopNodes) {
            const runNoLink = () => {
              animateNodes.forEach((n) => {
                if (n.attr('class') === 'noLink') {
                  n.transition()
                    .ease(d3.easeLinear)
                    .duration(duration)
                    .attr('cy', n.attr('endY') * 1 + 10)
                    .attr('fill-opacity', Math.random())
                    .transition()
                    .ease(d3.easeLinear)
                    .duration(duration)
                    .attr('cy', n.attr('endY') * 1 - 10)
                    .attr('fill-opacity', Math.random())
                    .on('end', runNoLink);
                }
              });
              animateTexts.forEach((n) => {
                if (n.attr('class') === 'noLink') {
                  n.transition()
                    .ease(d3.easeLinear)
                    .duration(duration)
                    .attr('y', n.attr('endY') * 1 + 10)
                    .transition()
                    .ease(d3.easeLinear)
                    .duration(duration)
                    .attr('y', n.attr('endY') * 1 - 10)
                    .on('end', runNoLink);
                }
              });
            };
            runNoLink();
          }
        }

        animateLinks.forEach((link) => {
          const sourceNode = svg.select(`#${link.attr('sourceId')}`);
          const targetNode = svg.select(`#${link.attr('targetId')}`);
          if (
            sourceNode.attr('r') >= nodeRadius && targetNode.attr('r') >= nodeRadius && link.attr('animateEnd') === 'false'
          ) {
            link
              .transition()
              .ease(d3.easeLinear)
              .duration(duration)
              .attr('stroke-opacity', linkStrokeOpacity)
              .attr('marker-end', 'url(#arrow)')
              .attr('x2', link.attr('endX'))
              .attr('y2', link.attr('endY'))
              .attr('animateEnd', 'true');
            const text = svg.select(`#${link.attr('sourceId')}-${link.attr('targetId')}`);
            text
              .transition()
              .ease(d3.easeLinear)
              .duration(duration)
              .delay(duration / 2)
              .attr('fill-opacity', 1);
          }
        });
      }, duration / 2);
      return svg.node();
    },
  },
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped lang="less">
.container {
  position: relative;
  height: 100%;
  background-color: #9dadc1;
}
</style>
