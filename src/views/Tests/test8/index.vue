<template>
  <div id="tree" class="tree" />

</template>

<script>
import * as d3 from 'd3'
const dataSet = {
  'name': '中国',
  'children':
    [
      {
        'name': '3B、',
        'children':
          [
            { 'name': '杭州',
              'children': [
                { 'name': '宁波',
                  'children': [
                    { 'name': '宁波',
                      'children': [
                        { 'name': '温州',
                          'children': [
                            { 'name': '温州',
                              'children': [
                                { 'name': '温州',
                                  'children': [
                                    { 'name': '宁波',
                                      'children': [
                                        { 'name': '温州',
                                          'children': [
                                            { 'name': '温州' },
                                            { 'name': '绍兴' }
                                          ]
                                        },
                                        { 'name': '绍兴' }
                                      ]
                                    },
                                    { 'name': '温州' },
                                    { 'name': '绍兴' }
                                  ]
                                },
                                { 'name': '绍兴' }
                              ]
                            },
                            { 'name': '绍兴' }
                          ]
                        },
                        { 'name': '绍兴' }
                      ]
                    },
                    { 'name': '温州' },
                    { 'name': '绍兴' }
                  ]
                },
                { 'name': '温州' }
              ]
            },
            { 'name': '宁波' },
            { 'name': '温州' },
            { 'name': '绍兴' }
          ]
      },

      {
        'name': '广西',
        'children':
          [
            {
              'name': '桂林',
              'children':
                [
                  { 'name': '秀峰区' },
                  { 'name': '叠彩区' },
                  { 'name': '象山区' },
                  { 'name': '七星区' },
                  {
                    'name': '浙江',
                    'children':
                      [
                        { 'name': '杭州',
                          'children': [
                            { 'name': '宁波',
                              'children': [
                                { 'name': '宁波',
                                  'children': [
                                    { 'name': '温州',
                                      'children': [
                                        { 'name': '温州',
                                          'children': [
                                            { 'name': '温州',
                                              'children': [
                                                { 'name': '宁波',
                                                  'children': [
                                                    { 'name': '温州',
                                                      'children': [
                                                        { 'name': '温州',
                                                          'children': [
                                                            { 'name': '温州' },
                                                            { 'name': '绍兴' }
                                                          ] },
                                                        { 'name': '绍兴' }
                                                      ] },
                                                    { 'name': '绍兴' }
                                                  ]
                                                },
                                                { 'name': '温州' },
                                                { 'name': '绍兴' }
                                              ]
                                            },
                                            { 'name': '绍兴' }
                                          ]
                                        },
                                        { 'name': '绍兴' }
                                      ]
                                    },
                                    { 'name': '绍兴' }
                                  ]
                                },
                                { 'name': '温州' },
                                { 'name': '绍兴' }
                              ]
                            },
                            { 'name': '温州' }
                          ]
                        },
                        { 'name': '宁波' },
                        { 'name': '温州' },
                        { 'name': '绍兴' }
                      ]
                  },
                  {
                    'name': '浙江',
                    'children':
                      [
                        { 'name': '杭州',
                          'children': [
                            { 'name': '宁波',
                              'children': [
                                { 'name': '宁波',
                                  'children': [
                                    { 'name': '温州',
                                      'children': [
                                        { 'name': '温州',
                                          'children': [
                                            { 'name': '温州',
                                              'children': [
                                                { 'name': '宁波',
                                                  'children': [
                                                    { 'name': '温州',
                                                      'children': [
                                                        { 'name': '温州',
                                                          'children': [
                                                            { 'name': '温州' },
                                                            { 'name': '绍兴' }
                                                          ] },
                                                        { 'name': '绍兴' }
                                                      ] },
                                                    { 'name': '绍兴' }
                                                  ]
                                                },
                                                { 'name': '温州' },
                                                { 'name': '绍兴' }
                                              ]
                                            },
                                            { 'name': '绍兴' }
                                          ]
                                        },
                                        { 'name': '绍兴' }
                                      ]
                                    },
                                    { 'name': '绍兴' }
                                  ]
                                },
                                { 'name': '温州' },
                                { 'name': '绍兴' }
                              ]
                            },
                            { 'name': '温州' }
                          ]
                        },
                        { 'name': '宁波' },
                        { 'name': '温州' },
                        { 'name': '绍兴' }
                      ]
                  },
                  {
                    'name': '浙江',
                    'children':
                      [
                        { 'name': '杭州',
                          'children': [
                            { 'name': '宁波',
                              'children': [
                                { 'name': '宁波',
                                  'children': [
                                    { 'name': '温州',
                                      'children': [
                                        { 'name': '温州',
                                          'children': [
                                            { 'name': '温州',
                                              'children': [
                                                { 'name': '宁波',
                                                  'children': [
                                                    { 'name': '温州',
                                                      'children': [
                                                        { 'name': '温州',
                                                          'children': [
                                                            { 'name': '温州' },
                                                            { 'name': '绍兴' }
                                                          ] },
                                                        { 'name': '绍兴' }
                                                      ] },
                                                    { 'name': '绍兴' }
                                                  ]
                                                },
                                                { 'name': '温州' },
                                                { 'name': '绍兴' }
                                              ]
                                            },
                                            { 'name': '绍兴' }
                                          ]
                                        },
                                        { 'name': '绍兴' }
                                      ]
                                    },
                                    { 'name': '绍兴' }
                                  ]
                                },
                                { 'name': '温州' },
                                { 'name': '绍兴' }
                              ]
                            },
                            { 'name': '温州' }
                          ]
                        },
                        { 'name': '宁波' },
                        { 'name': '温州' },
                        { 'name': '绍兴' }
                      ]
                  },
                  {
                    'name': '浙江',
                    'children':
                      [
                        { 'name': '杭州',
                          'children': [
                            { 'name': '宁波',
                              'children': [
                                { 'name': '宁波',
                                  'children': [
                                    { 'name': '温州',
                                      'children': [
                                        { 'name': '温州',
                                          'children': [
                                            { 'name': '温州',
                                              'children': [
                                                { 'name': '宁波',
                                                  'children': [
                                                    { 'name': '温州',
                                                      'children': [
                                                        { 'name': '温州',
                                                          'children': [
                                                            { 'name': '温州' },
                                                            { 'name': '绍兴' }
                                                          ] },
                                                        { 'name': '绍兴' }
                                                      ] },
                                                    { 'name': '绍兴' }
                                                  ]
                                                },
                                                { 'name': '温州' },
                                                { 'name': '绍兴' }
                                              ]
                                            },
                                            { 'name': '绍兴' }
                                          ]
                                        },
                                        { 'name': '绍兴' }
                                      ]
                                    },
                                    { 'name': '绍兴' }
                                  ]
                                },
                                { 'name': '温州' },
                                { 'name': '绍兴' }
                              ]
                            },
                            { 'name': '温州' }
                          ]
                        },
                        { 'name': '宁波' },
                        { 'name': '温州' },
                        { 'name': '绍兴' }
                      ]
                  },
                  {
                    'name': '浙江',
                    'children':
                      [
                        { 'name': '杭州',
                          'children': [
                            { 'name': '宁波',
                              'children': [
                                { 'name': '宁波',
                                  'children': [
                                    { 'name': '温州',
                                      'children': [
                                        { 'name': '温州',
                                          'children': [
                                            { 'name': '温州',
                                              'children': [
                                                { 'name': '宁波',
                                                  'children': [
                                                    { 'name': '温州',
                                                      'children': [
                                                        { 'name': '温州',
                                                          'children': [
                                                            { 'name': '温州' },
                                                            { 'name': '绍兴' }
                                                          ] },
                                                        { 'name': '绍兴' }
                                                      ] },
                                                    { 'name': '绍兴' }
                                                  ]
                                                },
                                                { 'name': '温州' },
                                                { 'name': '绍兴' }
                                              ]
                                            },
                                            { 'name': '绍兴' }
                                          ]
                                        },
                                        { 'name': '绍兴' }
                                      ]
                                    },
                                    { 'name': '绍兴' }
                                  ]
                                },
                                { 'name': '温州' },
                                { 'name': '绍兴' }
                              ]
                            },
                            { 'name': '温州' }
                          ]
                        },
                        { 'name': '宁波' },
                        { 'name': '温州' },
                        { 'name': '绍兴' }
                      ]
                  },
                  {
                    'name': '浙江',
                    'children':
                      [
                        { 'name': '杭州',
                          'children': [
                            { 'name': '宁波',
                              'children': [
                                { 'name': '宁波',
                                  'children': [
                                    { 'name': '温州',
                                      'children': [
                                        { 'name': '温州',
                                          'children': [
                                            { 'name': '温州',
                                              'children': [
                                                { 'name': '宁波',
                                                  'children': [
                                                    { 'name': '温州',
                                                      'children': [
                                                        { 'name': '温州',
                                                          'children': [
                                                            { 'name': '温州' },
                                                            { 'name': '绍兴' }
                                                          ] },
                                                        { 'name': '绍兴' }
                                                      ] },
                                                    { 'name': '绍兴' }
                                                  ]
                                                },
                                                { 'name': '温州' },
                                                { 'name': '绍兴' }
                                              ]
                                            },
                                            { 'name': '绍兴' }
                                          ]
                                        },
                                        { 'name': '绍兴' }
                                      ]
                                    },
                                    { 'name': '绍兴' }
                                  ]
                                },
                                { 'name': '温州' },
                                { 'name': '绍兴' }
                              ]
                            },
                            { 'name': '温州' }
                          ]
                        },
                        { 'name': '宁波' },
                        { 'name': '温州' },
                        { 'name': '绍兴' }
                      ]
                  },
                  {
                    'name': '浙江',
                    'children':
                      [
                        { 'name': '杭州',
                          'children': [
                            { 'name': '宁波',
                              'children': [
                                { 'name': '宁波',
                                  'children': [
                                    { 'name': '温州',
                                      'children': [
                                        { 'name': '温州',
                                          'children': [
                                            { 'name': '温州',
                                              'children': [
                                                { 'name': '宁波',
                                                  'children': [
                                                    { 'name': '温州',
                                                      'children': [
                                                        { 'name': '温州',
                                                          'children': [
                                                            { 'name': '温州' },
                                                            { 'name': '绍兴' }
                                                          ] },
                                                        { 'name': '绍兴' }
                                                      ] },
                                                    { 'name': '绍兴' }
                                                  ]
                                                },
                                                { 'name': '温州' },
                                                { 'name': '绍兴' }
                                              ]
                                            },
                                            { 'name': '绍兴' }
                                          ]
                                        },
                                        { 'name': '绍兴' }
                                      ]
                                    },
                                    { 'name': '绍兴' }
                                  ]
                                },
                                { 'name': '温州' },
                                { 'name': '绍兴' }
                              ]
                            },
                            { 'name': '温州' }
                          ]
                        },
                        { 'name': '宁波' },
                        { 'name': '温州' },
                        { 'name': '绍兴' }
                      ]
                  },
                  {
                    'name': '浙江',
                    'children':
                      [
                        { 'name': '杭州',
                          'children': [
                            { 'name': '宁波',
                              'children': [
                                { 'name': '宁波',
                                  'children': [
                                    { 'name': '温州',
                                      'children': [
                                        { 'name': '温州',
                                          'children': [
                                            { 'name': '温州',
                                              'children': [
                                                { 'name': '宁波',
                                                  'children': [
                                                    { 'name': '温州',
                                                      'children': [
                                                        { 'name': '温州',
                                                          'children': [
                                                            { 'name': '温州' },
                                                            { 'name': '绍兴' }
                                                          ] },
                                                        { 'name': '绍兴' }
                                                      ] },
                                                    { 'name': '绍兴' }
                                                  ]
                                                },
                                                { 'name': '温州' },
                                                { 'name': '绍兴' }
                                              ]
                                            },
                                            { 'name': '绍兴' }
                                          ]
                                        },
                                        { 'name': '绍兴' }
                                      ]
                                    },
                                    { 'name': '绍兴' }
                                  ]
                                },
                                { 'name': '温州' },
                                { 'name': '绍兴' }
                              ]
                            },
                            { 'name': '温州' }
                          ]
                        },
                        { 'name': '宁波' },
                        { 'name': '温州' },
                        { 'name': '绍兴' }
                      ]
                  },
                  {
                    'name': '浙江',
                    'children':
                      [
                        { 'name': '杭州',
                          'children': [
                            { 'name': '宁波',
                              'children': [
                                { 'name': '宁波',
                                  'children': [
                                    { 'name': '温州',
                                      'children': [
                                        { 'name': '温州',
                                          'children': [
                                            { 'name': '温州',
                                              'children': [
                                                { 'name': '宁波',
                                                  'children': [
                                                    { 'name': '温州',
                                                      'children': [
                                                        { 'name': '温州',
                                                          'children': [
                                                            { 'name': '温州' },
                                                            { 'name': '绍兴' }
                                                          ] },
                                                        { 'name': '绍兴' }
                                                      ] },
                                                    { 'name': '绍兴' }
                                                  ]
                                                },
                                                { 'name': '温州' },
                                                { 'name': '绍兴' }
                                              ]
                                            },
                                            { 'name': '绍兴' }
                                          ]
                                        },
                                        { 'name': '绍兴' }
                                      ]
                                    },
                                    { 'name': '绍兴' }
                                  ]
                                },
                                { 'name': '温州' },
                                { 'name': '绍兴' }
                              ]
                            },
                            { 'name': '温州' }
                          ]
                        },
                        { 'name': '宁波' },
                        { 'name': '温州' },
                        { 'name': '绍兴' }
                      ]
                  }
                ]
            },
            { 'name': '南宁' },
            { 'name': '柳州' },
            { 'name': '防城港' },
            {
              'name': '浙江',
              'children':
                [
                  { 'name': '杭州',
                    'children': [
                      { 'name': '宁波',
                        'children': [
                          { 'name': '宁波',
                            'children': [
                              { 'name': '温州',
                                'children': [
                                  { 'name': '温州',
                                    'children': [
                                      { 'name': '温州',
                                        'children': [
                                          { 'name': '宁波',
                                            'children': [
                                              { 'name': '温州',
                                                'children': [
                                                  { 'name': '温州',
                                                    'children': [
                                                      { 'name': '温州' },
                                                      { 'name': '绍兴' }
                                                    ] },
                                                  { 'name': '绍兴' }
                                                ] },
                                              { 'name': '绍兴' }
                                            ]
                                          },
                                          { 'name': '温州' },
                                          { 'name': '绍兴' }
                                        ]
                                      },
                                      { 'name': '绍兴' }
                                    ]
                                  },
                                  { 'name': '绍兴' }
                                ]
                              },
                              { 'name': '绍兴' }
                            ]
                          },
                          { 'name': '温州' },
                          { 'name': '绍兴' }
                        ]
                      },
                      { 'name': '温州' }
                    ]
                  },
                  { 'name': '宁波' },
                  { 'name': '温州' },
                  { 'name': '绍兴' }
                ]
            }
          ]
      },

      {
        'name': '黑龙江',
        'children':
          [
            { 'name': '哈尔滨' },
            { 'name': '齐齐哈尔' },
            { 'name': '牡丹江' },
            {
              'name': '浙江',
              'children':
                [
                  { 'name': '杭州',
                    'children': [
                      { 'name': '宁波',
                        'children': [
                          { 'name': '宁波',
                            'children': [
                              { 'name': '温州',
                                'children': [
                                  { 'name': '温州',
                                    'children': [
                                      { 'name': '温州',
                                        'children': [
                                          { 'name': '宁波',
                                            'children': [
                                              { 'name': '温州',
                                                'children': [
                                                  { 'name': '温州',
                                                    'children': [
                                                      { 'name': '温州' },
                                                      { 'name': '绍兴' }
                                                    ] },
                                                  { 'name': '绍兴' }
                                                ] },
                                              { 'name': '绍兴' }
                                            ]
                                          },
                                          { 'name': '温州' },
                                          { 'name': '绍兴' }
                                        ]
                                      },
                                      { 'name': '绍兴' }
                                    ]
                                  },
                                  { 'name': '绍兴' }
                                ]
                              },
                              { 'name': '绍兴' }
                            ]
                          },
                          { 'name': '温州' },
                          { 'name': '绍兴' }
                        ]
                      },
                      { 'name': '温州' }
                    ]
                  },
                  { 'name': '宁波' },
                  { 'name': '温州' },
                  { 'name': '绍兴' }
                ]
            },
            { 'name': '大庆' },
            {
              'name': '浙江',
              'children':
                [
                  { 'name': '杭州',
                    'children': [
                      { 'name': '宁波',
                        'children': [
                          { 'name': '宁波',
                            'children': [
                              { 'name': '温州',
                                'children': [
                                  { 'name': '温州',
                                    'children': [
                                      { 'name': '温州',
                                        'children': [
                                          { 'name': '宁波',
                                            'children': [
                                              { 'name': '温州',
                                                'children': [
                                                  { 'name': '温州',
                                                    'children': [
                                                      { 'name': '温州' },
                                                      { 'name': '绍兴' }
                                                    ] },
                                                  { 'name': '绍兴' }
                                                ] },
                                              { 'name': '绍兴' }
                                            ]
                                          },
                                          { 'name': '温州' },
                                          { 'name': '绍兴' }
                                        ]
                                      },
                                      { 'name': '绍兴' }
                                    ]
                                  },
                                  { 'name': '绍兴' }
                                ]
                              },
                              { 'name': '绍兴' }
                            ]
                          },
                          { 'name': '温州' },
                          { 'name': '绍兴' }
                        ]
                      },
                      { 'name': '温州' }
                    ]
                  },
                  { 'name': '宁波' },
                  { 'name': '温州' },
                  { 'name': '绍兴' }
                ]
            },
            {
              'name': '浙江',
              'children':
                [
                  { 'name': '杭州',
                    'children': [
                      { 'name': '宁波',
                        'children': [
                          { 'name': '宁波',
                            'children': [
                              { 'name': '温州',
                                'children': [
                                  { 'name': '温州',
                                    'children': [
                                      { 'name': '温州',
                                        'children': [
                                          { 'name': '宁波',
                                            'children': [
                                              { 'name': '温州',
                                                'children': [
                                                  { 'name': '温州',
                                                    'children': [
                                                      { 'name': '温州' },
                                                      { 'name': '绍兴' }
                                                    ] },
                                                  { 'name': '绍兴' }
                                                ] },
                                              { 'name': '绍兴' }
                                            ]
                                          },
                                          { 'name': '温州' },
                                          { 'name': '绍兴' }
                                        ]
                                      },
                                      { 'name': '绍兴' }
                                    ]
                                  },
                                  { 'name': '绍兴' }
                                ]
                              },
                              { 'name': '绍兴' }
                            ]
                          },
                          { 'name': '温州' },
                          { 'name': '绍兴' }
                        ]
                      },
                      { 'name': '温州' }
                    ]
                  },
                  { 'name': '宁波' },
                  { 'name': '温州' },
                  { 'name': '绍兴' }
                ]
            },
            {
              'name': '浙江',
              'children':
                [
                  { 'name': '杭州',
                    'children': [
                      { 'name': '宁波',
                        'children': [
                          { 'name': '宁波',
                            'children': [
                              { 'name': '温州',
                                'children': [
                                  { 'name': '温州',
                                    'children': [
                                      { 'name': '温州',
                                        'children': [
                                          { 'name': '宁波',
                                            'children': [
                                              { 'name': '温州',
                                                'children': [
                                                  { 'name': '温州',
                                                    'children': [
                                                      { 'name': '温州' },
                                                      { 'name': '绍兴' }
                                                    ] },
                                                  { 'name': '绍兴' }
                                                ] },
                                              { 'name': '绍兴' }
                                            ]
                                          },
                                          { 'name': '温州' },
                                          { 'name': '绍兴' }
                                        ]
                                      },
                                      { 'name': '绍兴' }
                                    ]
                                  },
                                  { 'name': '绍兴' }
                                ]
                              },
                              { 'name': '绍兴' }
                            ]
                          },
                          { 'name': '温州' },
                          { 'name': '绍兴' }
                        ]
                      },
                      { 'name': '温州' }
                    ]
                  },
                  { 'name': '宁波' },
                  { 'name': '温州' },
                  { 'name': '绍兴' }
                ]
            },
            {
              'name': '浙江',
              'children':
                [
                  { 'name': '杭州',
                    'children': [
                      { 'name': '宁波',
                        'children': [
                          { 'name': '宁波',
                            'children': [
                              { 'name': '温州',
                                'children': [
                                  { 'name': '温州',
                                    'children': [
                                      { 'name': '温州',
                                        'children': [
                                          { 'name': '宁波',
                                            'children': [
                                              { 'name': '温州',
                                                'children': [
                                                  { 'name': '温州',
                                                    'children': [
                                                      { 'name': '温州' },
                                                      { 'name': '绍兴' }
                                                    ] },
                                                  { 'name': '绍兴' }
                                                ] },
                                              { 'name': '绍兴' }
                                            ]
                                          },
                                          { 'name': '温州' },
                                          { 'name': '绍兴' }
                                        ]
                                      },
                                      { 'name': '绍兴' }
                                    ]
                                  },
                                  { 'name': '绍兴' }
                                ]
                              },
                              { 'name': '绍兴' }
                            ]
                          },
                          { 'name': '温州' },
                          { 'name': '绍兴' }
                        ]
                      },
                      { 'name': '温州' }
                    ]
                  },
                  { 'name': '宁波' },
                  { 'name': '温州' },
                  { 'name': '绍兴' }
                ]
            }
          ]
      },
      {
        'name': '浙江',
        'children':
          [
            { 'name': '杭州',
              'children': [
                { 'name': '宁波',
                  'children': [
                    { 'name': '宁波',
                      'children': [
                        { 'name': '温州',
                          'children': [
                            { 'name': '温州',
                              'children': [
                                { 'name': '温州',
                                  'children': [
                                    { 'name': '宁波',
                                      'children': [
                                        { 'name': '温州',
                                          'children': [
                                            { 'name': '温州',
                                              'children': [
                                                { 'name': '温州' },
                                                { 'name': '绍兴' }
                                              ] },
                                            { 'name': '绍兴' }
                                          ] },
                                        { 'name': '绍兴' }
                                      ]
                                    },
                                    { 'name': '温州' },
                                    { 'name': '绍兴' }
                                  ]
                                },
                                { 'name': '绍兴' }
                              ]
                            },
                            { 'name': '绍兴' }
                          ]
                        },
                        { 'name': '绍兴' }
                      ]
                    },
                    { 'name': '温州' },
                    { 'name': '绍兴' }
                  ]
                },
                { 'name': '温州' }
              ]
            },
            { 'name': '宁波' },
            { 'name': '温州' },
            { 'name': '绍兴' }
          ]
      },
      {
        'name': '浙江',
        'children':
          [
            { 'name': '杭州',
              'children': [
                { 'name': '宁波',
                  'children': [
                    { 'name': '宁波',
                      'children': [
                        { 'name': '温州',
                          'children': [
                            { 'name': '温州',
                              'children': [
                                { 'name': '温州',
                                  'children': [
                                    { 'name': '宁波',
                                      'children': [
                                        { 'name': '温州',
                                          'children': [
                                            { 'name': '温州',
                                              'children': [
                                                { 'name': '温州' },
                                                { 'name': '绍兴' }
                                              ] },
                                            { 'name': '绍兴' }
                                          ] },
                                        { 'name': '绍兴' }
                                      ]
                                    },
                                    { 'name': '温州' },
                                    { 'name': '绍兴' }
                                  ]
                                },
                                { 'name': '绍兴' }
                              ]
                            },
                            { 'name': '绍兴' }
                          ]
                        },
                        { 'name': '绍兴' }
                      ]
                    },
                    { 'name': '温州' },
                    { 'name': '绍兴' }
                  ]
                },
                { 'name': '温州' }
              ]
            },
            { 'name': '宁波' },
            { 'name': '温州' },
            { 'name': '绍兴' }
          ]
      },
      {
        'name': '浙江',
        'children':
          [
            { 'name': '杭州',
              'children': [
                { 'name': '宁波',
                  'children': [
                    { 'name': '宁波',
                      'children': [
                        { 'name': '温州',
                          'children': [
                            { 'name': '温州',
                              'children': [
                                { 'name': '温州',
                                  'children': [
                                    { 'name': '宁波',
                                      'children': [
                                        { 'name': '温州',
                                          'children': [
                                            { 'name': '温州',
                                              'children': [
                                                { 'name': '温州' },
                                                { 'name': '绍兴' }
                                              ] },
                                            { 'name': '绍兴' }
                                          ] },
                                        { 'name': '绍兴' }
                                      ]
                                    },
                                    { 'name': '温州' },
                                    { 'name': '绍兴' }
                                  ]
                                },
                                { 'name': '绍兴' }
                              ]
                            },
                            { 'name': '绍兴' }
                          ]
                        },
                        { 'name': '绍兴' }
                      ]
                    },
                    { 'name': '温州' },
                    { 'name': '绍兴' }
                  ]
                },
                { 'name': '温州' }
              ]
            },
            { 'name': '宁波' },
            { 'name': '温州' },
            { 'name': '绍兴' }
          ]
      },
      {
        'name': '浙江',
        'children':
          [
            { 'name': '杭州',
              'children': [
                { 'name': '宁波',
                  'children': [
                    { 'name': '宁波',
                      'children': [
                        { 'name': '温州',
                          'children': [
                            { 'name': '温州',
                              'children': [
                                { 'name': '温州',
                                  'children': [
                                    { 'name': '宁波',
                                      'children': [
                                        { 'name': '温州',
                                          'children': [
                                            { 'name': '温州',
                                              'children': [
                                                { 'name': '温州' },
                                                { 'name': '绍兴' }
                                              ] },
                                            { 'name': '绍兴' }
                                          ] },
                                        { 'name': '绍兴' }
                                      ]
                                    },
                                    { 'name': '温州' },
                                    { 'name': '绍兴' }
                                  ]
                                },
                                { 'name': '绍兴' }
                              ]
                            },
                            { 'name': '绍兴' }
                          ]
                        },
                        { 'name': '绍兴' }
                      ]
                    },
                    { 'name': '温州' },
                    { 'name': '绍兴' }
                  ]
                },
                { 'name': '温州' }
              ]
            },
            { 'name': '宁波' },
            { 'name': '温州' },
            { 'name': '绍兴' }
          ]
      },

      {
        'name': '新疆',
        'children':
          [
            { 'name': '乌鲁木齐' },
            { 'name': '克拉玛依' },
            { 'name': '吐鲁番' },
            { 'name': '哈密' }
          ]
      }
    ]
}
export default {
  data() {
    return {
      index: 0,
      duration: 750,
      root: null
    }
  },
  mounted() {
    this.width = document.getElementById('tree').offsetWidth
    this.height = document.getElementById('tree').offsetHeight

    this.zoom = d3.behavior.zoom().scaleExtent([0.1, 100]).on('zoom', this.zoomed)
    this.svg = d3.select('body').select('#tree').append('svg')
      .call(this.zoom)
      .append('g')
      .call(this.zoom)
      .append('g')
      .attr('width', 1560)
      .attr('height', 800)
      .attr('transform', 'translate(100,0)')

    this.root = this.getNode()
    this.update(this.root)
  },
  methods: {
    getNode() {
      this.root = dataSet
      this.root.x0 = this.height / 2
      this.root.y0 = 0

      this.root.children.forEach(this.collapse)
      return this.root
    },

    zoomed() {
      this.svg.attr('transform',
        'translate(' + this.zoom.translate() + ')' +
        'scale(' + this.zoom.scale() + ')'
      )
    },

    collapse(d) {
      // console.log(d)
      if (d.children) {
        d._children = d.children
        d._children.forEach(this.collapse)
        d.children = null
        // console.log(3)
      }
    },

    toggle(d) {
      // console.log(d)
      if (d.children) {
        console.log(1)
        d._children = d.children
        d.children = null
      } else {
        d.children = d._children
        d._children = null
        console.log(2)
      }
      this.update(d)
    },

    update(source) {
      const tree = d3.layout.tree().size([this.width, this.height])
      var nodes = tree.nodes(this.root).reverse()
      var links = tree.links(nodes)

      // console.log(nodes)

      nodes.forEach(function(d) {
        d.y = d.depth * 180
      })
      var node = this.svg.selectAll('g.node')
        .data(nodes, d => {
          return d.id || (d.id = ++this.index)
        })
      // console.log(source.x0)

      // console.log(nodes)
      var nodeEnter = node.enter().append('g')
        .attr('class', 'node')
        .attr('transform', d => {
          // console.log(d) // 打印所展示的
          // console.log(i)
          return 'translate(' + source.y0 + ',' + source.x0 + ')'
        })
        .on('click', this.toggle)
      // console.log(nodeEnter) //4个数组

      // const img_w = 20
      // const img_h = 20
      // nodeEnter.append('image')
      //   .attr('width', img_w)
      //   .attr('height', img_h)
      //   .attr('y', -10)
      //   .attr('x', -5)
      //   .attr('xlink:href')
      nodeEnter
        .append('circle')
        .attr('r', 1e-6)
        .style('fill', (d) => {
          return d._children ? '#f00' : '#fff'
        })
      // nodeEnter.append('circle')
      //   .attr('r', 0)
      //   .style('fill', function(d) { return d._children ? 'lightsteelblue' : '#fff' })

      nodeEnter.append('text')
        .attr('x', function(d) { return d.children || d._children ? -14 : 14 })
        .attr('dy', '.35em')
        .attr('text-anchor', function(d) { return d.children || d._children ? 'end' : 'start' })
        .text(function(d) { return d.name })
        .style('fill-opacity', 0)

      nodeEnter.append('text')
        .attr('x', function(d) { return d.children || d._children ? 20 : 0 })
        .attr('y', function(d) { return d.children || d._children ? 25 : 20 })
        .attr('dy', '.35em')
        .attr('text-anchor', function(d) { return d.children || d._children ? 'end' : 'start' })
        .text(function(d) { return d.name })
        .style('fill-opacity', 1)
        .style('font-size', '12px')

      // console.log(this.root)
      var nodeUpdate = node.transition()
        .duration(this.duration)
        .attr('transform', d => {
          return 'translate(' + d.y + ',' + d.x + ')'
        })

      nodeUpdate.select('circle')
        .attr('r', 8)
        .style('fill', function(d) { return d._children ? 'lightsteelblue' : '#fff' })

      nodeUpdate.select('text')
        .style('fill-opacity', 1)

      var exitNodes = node.exit().transition()
        .duration(this.duration)
        .attr('transform', d => {
          return 'translate(' + source.y + ',' + source.x + ')'
        })
        .remove()

      exitNodes.select('circle')
        .attr('r', 0)

      exitNodes.select('text')
        .style('fill-opacity', 0)

      var linkUpdate = this.svg.selectAll('.link')
        .data(links, d => { return d.target.id })

      var linkEnter = linkUpdate.enter()

      var linkExit = linkUpdate.exit()

      var diagonal = d3.svg.diagonal()
        .projection(d => { return [d.y, d.x] })

      linkEnter.insert('path', '.node')
        .attr('class', 'link')
        .attr('d', d => {
          var o = { x: source.x0, y: source.y0 }
          return diagonal({ source: o, target: o })
        })
        .transition()
        .duration(this.duration)
        .attr('d', diagonal)

      linkUpdate.transition()
        .duration(this.duration)
        .attr('d', diagonal)

      linkExit.transition()
        .duration(this.duration)
        .attr('d', d => {
          var o = { x: source.x, y: source.y }
          return diagonal({ source: o, target: o })
        })
        .remove()

      nodes.forEach(function(d) {
        d.x0 = d.x
        d.y0 = d.y
      })
    }
  }

}
</script>

<style>
.tree{
  width: 100%;
  height: 1000px;
  background: #f8f8fb;
}
.node {
  cursor: pointer;
}

.node circle {
  fill: none;
  stroke: #fff;
  stroke-width: 1.5px;
}

.node text {
  font: 10px sans-serif;
}

.link {
  fill: none;
  stroke: #999;
  stroke-width: 1.5px;
}

.tree svg {
  width: 100%;
  height: 100%;
}

</style>
