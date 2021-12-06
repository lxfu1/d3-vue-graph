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
          id: '数澜医院',
          name: '数澜医院',
          color: '#CA63F5',
          x: 0.7,
          y: 0.65,
        },
        {
          id: '张三',
          name: '张三',
          color: '#AAAAFF', // 不知道匹配规则，直接写死
          x: 0.5,
          y: 0.55,
        },
        {
          id: '李四',
          name: '李四',
          color: '#AAAAFF',
          x: 0.2,
          y: 0.65,
        },
        {
          id: '高支模',
          name: '高支模',
          color: '#AAAAFF',
          x: 0.6,
          y: 0.85,
        },
        {
          id: '山东',
          name: '山东',
          color: '#AAAAFF',
          x: 0.85,
          y: 0.35,
        },
        {
          id: '水泥',
          name: '水泥',
          color: '#55CCCC',
          x: 0.8,
          y: 0.78,
        },
        {
          id: '安全员',
          name: '安全员',
          color: '#55CCCC',
          x: 0.6,
          y: 0.35,
        },
        {
          id: '施工员',
          name: '施工员',
          color: '#55CCCC',
          x: 0.34,
          y: 0.7,
        },
        {
          id: '高温雨季',
          name: '高温雨季',
          color: '#55CCCC',
          x: 0.54,
          y: 0.78,
        },
        {
          id: '安全专项',
          name: '安全专项',
          color: '#CA63F5',
          x: 0.76,
          y: 0.45,
        },
        {
          id: '中建八局',
          name: '中建八局',
          color: '#CA63F5',
          x: 0.33,
          y: 0.43,
        },
        {
          id: '道路安全规范',
          name: '道路安全规范',
          color: '#CA63F5',
          x: 0.6,
          y: 0.5,
        },
      ],
      links: [
        {
          source: '水泥',
          target: '数澜医院',
        },
        {
          source: '安全员',
          target: '施工员',
        },
        {
          source: '高温雨季',
          target: '安全专项',
        },

        {
          source: '中建八局',
          target: '道路安全规范',
        },
        {
          source: '施工员',
          target: '数澜医院',
        },
        {
          source: '安全员',
          target: '数澜医院',
        },
        {
          source: '安全专项',
          target: '数澜医院',
        },
        {
          source: '中建八局',
          target: '数澜医院',
        },
        {
          source: '中建八局',
          target: '安全员',
        },
        {
          source: '中建八局',
          target: '施工员',
        },
      ],
      svg: null,
    };
  },
  mounted() {
    // this.processDara(STATISTICDATA);
    const { nodes, links } = this;
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
        width = 800, // outer width, in pixels
        height = 800, // outer height, in pixels
        radius = 250, // layout radius
        duration = 1000, // animation duration time(ms)
        showArrows = true, // whether to show end arrow
      } = {},
    ) {
      const heightLightNode = ['数澜医院'];
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
      const randomRadius = radius / 4;
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
          y: width * node.y,
        };
      });
      /** Loop processing edge, need to draw to the lowest level */
      originLinks.forEach((link, index) => {
        const { source, target } = link;
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
