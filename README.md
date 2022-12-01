
import React, {PureComponent} from 'react';
import {PieChart, Pie, Sector, ResponsiveContainer} from 'recharts';
import {NoDataInRootTableMessage} from '../../CommonComponents';


const renderActiveShape = props => {
    const RADIAN = Math.PI / 180;
    const {cx, cy, midAngle, innerRadius, outerRadius, startAngle, endAngle, fill, payload, percent, value} = props;
    const sin = Math.sin(-RADIAN * midAngle);
    const cos = Math.cos(-RADIAN * midAngle);
    const sx = cx + (outerRadius + 10) * cos;
    const sy = cy + (outerRadius + 10) * sin;
    const mx = cx + (outerRadius + 30) * cos;
    const my = cy + (outerRadius + 30) * sin;
    const ex = mx + (cos >= 0 ? 1 : -1) * 22;
    const ey = my;
    const textAnchor = cos >= 0 ? 'start' : 'end';

    return (
        <g>
            <text x={cx} y={cy * 0.95} dy={8} textAnchor="middle" fill={fill}>
                {payload.name}
            </text>
            <text x={cx} y={cy * 0.95} dy={25} textAnchor="middle" fill="#000">
                {`(Total: ${payload.total_records})`}
            </text>
            <Sector cx={cx} cy={cy} innerRadius={innerRadius} outerRadius={outerRadius} startAngle={startAngle} endAngle={endAngle} fill={fill} />
            <Sector cx={cx} cy={cy} startAngle={startAngle} endAngle={endAngle} innerRadius={outerRadius + 6} outerRadius={outerRadius + 10} fill={fill} />
            <path d={`M${sx},${sy}L${mx},${my}L${ex},${ey}`} stroke={fill} fill="none" />
            <circle cx={ex} cy={ey} r={2} fill={fill} stroke="none" />
            <text x={ex + (cos >= 0 ? 1 : -1) * 12} y={ey} textAnchor={textAnchor} fill={fill}>{`${percent}%`}</text>
            <text x={ex + (cos >= 0 ? 1 : -1) * 12} y={ey} dy={18} textAnchor={textAnchor} fill={fill}>
                {`(Job Count: ${value})`}
            </text>
        </g>
    );
};

export default class AllJobsResultAnalyticsPie extends PureComponent {
    state = {
        activeIndex: 0,
    };

    onPieEnter = (_, index) => {
        this.setState({
            activeIndex: index,
        });
    };

    render() {
        return !this.props.getAllDsJobsReportPieData || !this.props.getAllDsJobsReportPieData.data ? (
            <div className="error-message all-reports ds-jobs-pie">
                <NoDataInRootTableMessage key="allReports_dspie" headers={{job_name: 'Job Name'}} jobString="downstream job" parentType="segment" />
            </div>
        ) : (
            <ResponsiveContainer width={'99%'} height={285}>
                <PieChart width={800} height={400}>
                    <Pie
                        activeIndex={this.state.activeIndex}
                        activeShape={renderActiveShape}
                        data={this.props.getAllDsJobsReportPieData.data}
                        cx="50%"
                        cy="50%"
                        innerRadius={60}
                        outerRadius={90}
                        fill="#8884d8"
                        dataKey="value"
                        onMouseEnter={this.onPieEnter}
                    />
                </PieChart>
            </ResponsiveContainer>
        );
    }
}
