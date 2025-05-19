{% extends 'base.html' %}
{% load static %}
{% block title %}Detalhes da Cidade{% endblock %}
{% block content %}
{% for item in items %}
    <div class="json-container" style="font-family: monospace; padding: 20px; background-color: #000; color: #fff; border-radius: 5px;">
        <div style="margin-left: 20px;">"city": <span style="color: #adff2f;">"{{ item.city }}"</span>,</div>
        
        <div style="margin-left: 20px;">"cluster": [</div>
        {% for array in item.cluster.values %}
            <div style="margin-left: 80px;margin-top: 10px;">
            {% for ip_address in array %}
                <div style="margin-left: 0px;"><span style="color: #adff2f;">"{{ ip_address }}"</span>{% if not forloop.last %},{% endif %}</div>
            {% endfor %}
            </div>
        {% endfor %}
        <div style="margin-left: 20px;">],</div>
        
        <div style="margin-left: 20px;">"demarcation":</div>
        {% for key, value in item.demarcation.items %}
            <div style="margin-left: 40px; margin-top: 5px;">"{{ key }}": 
            {% if value|stringformat:"s"|first == "{" %}
                {% for subkey, subvalue in value.items %}
                    <div style="margin-left: 20px;">"{{ subkey }}": <span style="color: #adff2f;">"{{ subvalue }}"</span>{% if not forloop.last %},{% endif %}</div>
                {% endfor %}
            {% else %}
                <span style="color: #adff2f;">"{{ value }}"</span>
            {% endif %}
            {% if not forloop.last %},{% endif %}
            </div>
        {% endfor %}
        
        <div style="margin-left: 20px; margin-top: 10px;">"federative_Unit": <span style="color: #adff2f;">"{{ item.federative_Unit }}"</span>,</div>
        
        <div style="margin-left: 20px;">"nacional":</div>
        {% for key, value in item.nacional.items %}
            <div style="margin-left: 40px; margin-top: 5px;">"{{ key }}": 
            {% if value|stringformat:"s"|first == "[" %}
                <div style="margin-left: 80px;margin-top: 10px;">
                {% for ip_address in value %}
                    <div><span style="color: #adff2f;">"{{ ip_address }}"</span>{% if not forloop.last %},{% endif %}</div>
                {% endfor %}
                </div>
            {% else %}
                <span style="color: #adff2f;">"{{ value }}"</span>
            {% endif %}
            {% if not forloop.last %},{% endif %}
            </div>
        {% endfor %}
        
        <div style="margin-left: 20px; margin-top: 10px;">"nlc": <span style="color: #adff2f;">"{{ item.nlc }}"</span></div>
    </div>
{% endfor %}
{% endblock %}
