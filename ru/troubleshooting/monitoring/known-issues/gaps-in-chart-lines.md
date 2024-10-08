# Графики мониторинга имеют разрывы и пустоты при нормальной работе облачных ресурсов


## Описание проблемы {#issue-description}

На странице мониторинга облачного ресурса наблюдаются пустоты и прерывания линии одного или нескольких графиков, однако сам облачный ресурс работает корректно.

## Решение {#issue-resolution}

Прерывание линии графика мониторинга связано с особенностями его отображения для определенных типов облачных ресурсов. Такая ситуация может наблюдаться, если в настройках отображения графика включено прореживание — эта технология существенно ускоряет отрисовку странице при агрегации на ней большого количества метрик мониторинга. 

Однако если в мониторинг поступают низкие значения определенных метрик, которые отображаются на оси `Y`, в линии графика с включенным прореживанием могут появиться разрывы. Как правило, такие разрывы не сигнализируют о проблемах с облачным ресурсом.

Вы можете отключить прореживание при просмотре графика в сервисе {{ monitoring-name }}. Для этого выполните следующие действия:

1. Перейдите в раздел **{{ ui-key.yacloud.common.monitoring }}** со страницы интересующего вас облачного ресурса.

1. Нажмите на кнопку **{{ ui-key.yacloud_monitoring.dashboard.dash.open-in-monitoring }}** в правом верхнем углу страницы:
![image](../../../_assets/troubleshooting/monitoring/open-in-monitoring-highlight.png)

1. Нажмите на кнопку справа от наименования интересующего вас графика:
![image](../../../_assets/troubleshooting/monitoring/compass-button-highlight.png)

1. Перейдите на вкладку **{{ ui-key.yacloud_monitoring.wizard.tab.downsampling }}** и выберите для поля **{{ ui-key.yacloud_monitoring.channel.field_method }}** значение **{{ ui-key.yacloud_monitoring.wizard.vis.downsampling.none }}**:
![image](../../../_assets/troubleshooting/monitoring/disable-thinning.png)
