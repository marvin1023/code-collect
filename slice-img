const sharp = require('sharp');

const sizeOf = require('image-size');

const currentImageInfo = sizeOf('./input2.png');

const { width: picWidth, height: picHeight } = currentImageInfo;

const DEFAULT_PIECE_WIDTH = 600;
const DEFAULT_PIECE_HEIGHT = 600;

const slicePiece = (options) => {
  // 每个裁剪块的对象
  // {width, height, left, top,  row, column}
  const { originValue, sliceValue, fillValue, type = 'x' } = options;
  let { left = 0, top = 0, row = 1, column = 1 } = options;
  const arr = [];

  // 总碎片数，最后一个碎片的尺寸
  const totalPiece = Math.ceil(originValue / sliceValue);
  const lastPieceValue = originValue % sliceValue;

  //
  for (let i = 0; i < totalPiece; i++) {
    const newSliceValue = i === totalPiece - 1 ? lastPieceValue : sliceValue;

    if (type === 'x') {
      arr.push({ width: newSliceValue, height: fillValue, left, top, row, column });
      left += sliceValue;
      column += 1;
    } else {
      arr.push({ width: fillValue, height: newSliceValue, left, top, row, column });
      top += sliceValue;
      row += 1;
    }
  }

  return arr;
};
const getImagePieceData = ({
  picWidth,
  picHeight,
  sliceWidth = DEFAULT_PIECE_WIDTH,
  sliceHeight = DEFAULT_PIECE_HEIGHT,
}) => {
  // 行列数
  const columns = Math.ceil(picWidth / sliceWidth);
  const rows = Math.ceil(picHeight / sliceHeight);
  const lastRowHeight = picHeight % sliceHeight;
  const lastColumnWidth = picWidth % sliceWidth;
  const list = [];

  const data = {
    columns,
    rows,
    lastRowHeight,
    lastColumnWidth,
    type: 'none',
    list,
  };

  // 没有裁剪
  if (rows === 1 && columns === 1) {
    return data;
  }

  // 宽度裁剪，高度不裁剪
  if (rows === 1 && columns !== 1) {
    const columnList = slicePiece({ originValue: picWidth, sliceValue: sliceWidth, fillValue: picHeight, type: 'x' });

    data.type = 'x';
    data.list.push(...columnList);

    return data;
  }

  // 宽度不裁剪，高度裁剪
  if (columns === 1 && rows !== 1) {
    const rowList = slicePiece({ originValue: picHeight, sliceValue: sliceHeight, fillValue: picWidth, type: 'y' });

    data.type = 'y';
    data.list.push(...rowList);

    return data;
  }

  // 宽高都裁剪
  // 前面 rows - 1 行
  for (let i = 1; i <= rows; i++) {
    const levelPiece = slicePiece({
      originValue: picWidth,
      sliceValue: sliceWidth,
      fillValue: i === rows ? lastRowHeight : sliceHeight,
      type: 'x',
      top: (i - 1) * sliceHeight,
      row: i,
    });

    data.list.push(...levelPiece);
  }

  data.type = 'xy';

  return data;
};

const sliceArr = getImagePieceData({ picWidth, picHeight });

// console.log(sliceArr, 'sliceArr');

sliceArr.list.forEach(({ left, top, width, height, row, column }, index) => {
  sharp('./input2.png')
    .extract({ left, top, width, height })
    .toFile(`./img/${row}_${column}.png`)
    .then((info) => {
      // console.log(`split_${row}_${column}_${width}_${height}.jpg切割成功`);
    })
    .catch((err) => {
      console.log(JSON.stringify(err), 'error');
    });
});
